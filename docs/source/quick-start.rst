Quick start
===============

.. _quick-start:

.. _installation:

1. Installation
--------------------

Install `tfts <https://github.com/LongxingTan/Time-series-prediction>`_, follow the installation instructions first

* Python 3.7+
* `TensorFlow 2.x <https://www.tensorflow.org/install/pip>`_ installation instructions

Now you are ready, proceed with

.. code-block:: shell

    $ pip install tfts


You can run it in docker, download the Dockerfile to host server

.. code-block:: shell

    $ docker build -f ./Dockerfile -t "custom image name" .
    $ docker run --rm -it --init --ipc=host --network=host --volume=$PWD:/app -e NVIDIA_VISIBLE_DEVICES=0 "custom image name" /bin/bash

.. _usage:

2. Basic Usage
-----------------------

.. currentmodule:: tfts

The general setup for training and testing a model is

#. Build time series 3D training dataset and valid dataset. The shape of input and label are (examples, train_sequence_length, features) and (examples, predict_sequence_length, 1)
#. Instantiate a model using the the ``AutoModel`` method
#. Create a ``Trainer()`` or ``KerasTrainer()`` object. Define the optimizer and loss function in trainer
#. Train the model on the training dataset and check if it has converged with acceptable accuracy
#. Tune the hyper-parameters of the model and training
#. Load the model from the model checkpoint and apply it to new data


.. code-block:: python

    import tensorflow as tf
    import tfts
    from tfts import AutoConfig, AutoModel, KerasTrainer

    train_length = 36
    predict_sequence_length = 12
    train, valid = tfts.get_data('sine', train_length, predict_sequence_length)

    # build model: 'seq2seq', 'wavenet', 'transformer', 'rnn', 'tcn', 'bert'
    model_name_or_path = 'seq2seq'
    config = AutoConfig.for_model(model_name_or_path)
    model = AutoModel.from_config(config, predict_sequence_length=predict_sequence_length)

    # train
    opt = tf.keras.optimizers.Adam(0.001)
    loss_fn = tf.keras.losses.MeanSquaredError()
    trainer = KerasTrainer(model, loss_fn=loss_fn, optimizer=opt)
    trainer.train(train, valid, epochs=30, batch_size=32)

    # test
    trainer.predict(valid[0])


3. Train your first model
------------------------------

3.1 Prepare the data
~~~~~~~~~~~~~~~~~~~~~~~~
Before training, ensure your raw data is preprocessed into a 3D format with the shape `(batch_size, train_steps, features)`. Perform any necessary data cleaning, normalization, or transformation steps to ensure the data is ready for training.


3.2 Train the Model
~~~~~~~~~~~~~~~~~~~~~~~~~~
When training the model, use appropriate loss functions, optimizers, and hyperparameters to achieve the best results.


3.3 Evaluate the model
~~~~~~~~~~~~~~~~~~~~~~~


3.4 Serve the model
~~~~~~~~~~~~~~~~~~~~~~~
Once the model is trained and evaluated, deploy it for inference. Ensure the model is saved in a format compatible with your serving environment (e.g., TensorFlow SavedModel, ONNX, etc.). Set up an API or service to handle incoming requests, preprocess input data, and return predictions in real-time.


.. currentmodule:: tfts
