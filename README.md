# SMS Spam Detection Using Machine Learning

This project is used a runnning example in the course **Release Engineering for Machine Learning Applications** (REMLA) taught at the Delft University of Technology by [Prof. Luís Cruz] and [Prof. Sebastian Proksch].

[Prof. Luís Cruz]: https://luiscruz.github.io/
[Prof. Sebastian Proksch]: https://proks.ch/

The codebase was originally adapted from: https://github.com/rohan8594/SMS-Spam-Detection

This project is a simply machine-learning project that illustrates how to detect spam messages.
The following guide will explain you how a model can be trained and served.
Please note that we have adapted the original project, the instructions only work with our extension.

The project **requires a Python 3.12 environment** to run (tested with 3.12.9).


## Training

In contrast to the original project, we provide a `requirements.txt` file that can be used to restore the required dependencies in your environment.

To train the model, you have two options.
Either you create a local environment...

    $ python -m venv venv
    $ source venv/bin/activate
    $ pip install -r requirements.txt

... or you can prepare a Docker container for the training:

    $ docker run -it --rm -v ./:/root/sms/ python:3.12.9-slim bash
    ... (container gets started)
    $ cd /root/sms/
    $ pip install -r requirements.txt

Once all dependencies have been installed, the data can be preprocessed and the model trained by creating the output folder and invoking three commands:

    $ mkdir output
    $ python src/read_data.py
    Total number of messages:5574
    ...
    $ python src/text_preprocessing.py
    [nltk_data] Downloading package stopwords to /root/nltk_data...
    [nltk_data]   Unzipping corpora/stopwords.zip.
    ...
    $ python src/text_classification.py

The resulting model files will be placed as `.joblib` files in the `output/` folder.



## Serving Recommendations

The trained `.joblib` model files can be used by Python programs.
However, we want to use it cross-language in a distributed application, as such, it is more convenient to wrap it in a micro service.
You can find a Dockerfile in the project, which contains all required steps for training the model, embedding the model in the image, and for preparing an executable application.

First build the image (the "." is important):

    docker build -t smsapp .

Afterwards, you can execute it 

    docker run -it --rm -p8080:8080 smsapp

Once the application is started, you can either access `localhost:8080/apidocs` with your browser or send `POST` requests to `localhost:8080/predict` to request predictions.
