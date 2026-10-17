Build a classifier to classify images into one of three classes/labels. The dataset Download datasetconsists of three folders (cats, dogs, and panda), containing images in different dimensions. Choose one of the following options to build your classifier.

Option 1: Build a model based on a convolutional neural network. For a classification problem, you usually choose softmax as the output activation function and categorical_crossentropy as the loss function.

Option 2: Build a model from a pre-trained model using transfer learning ( VGG 19 model, for example). Adapt your model to the image size (for example, 150x150x3) and outputs (the Keras API already has a list of built-in pretrained deep networks).


Notes and Guidelines
As in all deep neural networks, it is difficult to find the optimal CNN model. That's why we usually build one or more neural network models with different architectures  (i.e., different number of layers, different activation functions, and/or different number of neurons per layer) and different hyperparameter values (i.e., learning rates, number of epochs, batch size, optimizer (Adam, RMSProp, SGD) ). For this assignment, start by building a baseline model and fitting it to your training and testing datasets. Based on the evaluation of your loss/accuracy plots, you may decide to improve the model accuracy by varying the neural network architecture and/or hyper-parameter values ( please refer to the lessons and their case-studies in Keras for examples).

Regardless of your choice of option 1 or option 2, try to improve the accuracy of the model. Another important point is the data exploration. Before training your model, you should get insights from your dataset (number of images, view show couple of images, and specify the image size to use in the training). This is an indispensable and systematic task in any data analytics or machine learning project.