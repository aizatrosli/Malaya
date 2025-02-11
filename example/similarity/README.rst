
.. code:: ipython3

    %%time
    import malaya


.. parsed-literal::

    CPU times: user 4.24 s, sys: 633 ms, total: 4.88 s
    Wall time: 4.87 s


.. code:: ipython3

    string1 = 'Pemuda mogok lapar desak kerajaan prihatin isu iklim'
    string2 = 'Perbincangan isu pembalakan perlu babit kerajaan negeri'
    string3 = 'kerajaan perlu kisah isu iklim, pemuda mogok lapar'
    string4 = 'Kerajaan dicadang tubuh jawatankuasa khas tangani isu alam sekitar'

Calculate similarity using doc2vec
----------------------------------

We can use any word vector interface provided by Malaya to use doc2vec
similarity interface.

Important parameters, 1. ``aggregation``, aggregation function to
accumulate word vectors. Default is ``mean``.

::

   * ``'mean'`` - mean.
   * ``'min'`` - min.
   * ``'max'`` - max.
   * ``'sum'`` - sum.
   * ``'sqrt'`` - square root.

2. ``similarity`` distance function to calculate similarity. Default is
   ``cosine``.

   -  ``'cosine'`` - cosine similarity.
   -  ``'euclidean'`` - euclidean similarity.
   -  ``'manhattan'`` - manhattan similarity.

Using word2vec
^^^^^^^^^^^^^^

I will use ``load_news``, word2vec from wikipedia took a very long time.
wikipedia much more accurate.

.. code:: ipython3

    embedded_news = malaya.word2vec.load_news(256)
    w2v_wiki = malaya.word2vec.word2vec(embedded_news['nce_weights'],
                                        embedded_news['dictionary'])
    doc2vec = malaya.similarity.doc2vec(w2v_wiki)

predict for 2 strings
^^^^^^^^^^^^^^^^^^^^^

.. code:: ipython3

    doc2vec.predict(string1, string2, aggregation = 'mean', soft = False)




.. parsed-literal::

    0.8368814



predict batch of strings
^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: ipython3

    doc2vec.predict_batch([string1, string2], [string3, string4])




.. parsed-literal::

    array([0.9507282 , 0.88227606], dtype=float32)



visualize tree plot
^^^^^^^^^^^^^^^^^^^

.. code:: ipython3

    doc2vec.tree_plot([string1, string2, string3, string4])



.. parsed-literal::

    <Figure size 504x504 with 0 Axes>



.. image:: load-similarity_files/load-similarity_10_1.png


Different similarity function different percentage.

**So you can try use fast-text and elmo to do the similarity study.**

Calculate similarity using deep encoder
---------------------------------------

We can use any encoder models provided by Malaya to use encoder
similarity interface, example, BERT, XLNET, and skip-thought. Again,
these encoder models not trained to do similarity classification, it
just encode the strings into vector representation.

Important parameters,

1. ``similarity`` distance function to calculate similarity. Default is
   ``cosine``.

   -  ``'cosine'`` - cosine similarity.
   -  ``'euclidean'`` - euclidean similarity.
   -  ``'manhattan'`` - manhattan similarity.

using xlnet
^^^^^^^^^^^

.. code:: ipython3

    xlnet = malaya.xlnet.xlnet(model = 'small')
    encoder = malaya.similarity.encoder(xlnet)


.. parsed-literal::

    INFO:tensorflow:memory input None
    INFO:tensorflow:Use float type <dtype: 'float32'>
    INFO:tensorflow:Restoring parameters from /Users/huseinzol/Malaya/xlnet-model/small/xlnet-bahasa-small/model.ckpt


predict for 2 strings
^^^^^^^^^^^^^^^^^^^^^

.. code:: ipython3

    encoder.predict(string1, string2)




.. parsed-literal::

    0.9589016



predict batch of strings
^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: ipython3

    encoder.predict_batch([string1, string2], [string3, string4])




.. parsed-literal::

    array([0.97005975, 0.9447437 ], dtype=float32)



visualize tree plot
^^^^^^^^^^^^^^^^^^^

.. code:: ipython3

    encoder.tree_plot([string1, string2, string3, string4])



.. parsed-literal::

    <Figure size 504x504 with 0 Axes>



.. image:: load-similarity_files/load-similarity_21_1.png


BERT model
----------

BERT is the best similarity model in term of accuracy, you can check
similarity accuracy here,
https://malaya.readthedocs.io/en/latest/Accuracy.html#similarity.
Question is, why BERT?

1. Transformer model learn the context of a word based on all of its
   surroundings (live string), bidirectionally. So it much better
   understand left and right hand side relationships.
2. Because of transformer able to leverage to context during live
   string, we dont need to capture available words in this world,
   instead capture substrings and build the attention after that. BERT
   will never have Out-Of-Vocab problem.

List available BERT models
--------------------------

.. code:: ipython3

    malaya.similarity.available_bert_model()




.. parsed-literal::

    ['multilanguage', 'base', 'small']



.. code:: ipython3

    model = malaya.similarity.bert(model = 'base')

.. code:: ipython3

    model.predict(string1, string3)




.. parsed-literal::

    0.6755152



.. code:: ipython3

    model.predict_batch([string1, string2], [string3, string4])




.. parsed-literal::

    array([0.03622618, 0.03146545], dtype=float32)



**BERT is the best!**
