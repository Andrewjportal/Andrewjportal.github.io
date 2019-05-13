---
layout: post
title: DecadeStyles
---
***
## Overview
For this task, I wanted to build a clothing style classifier by decade. I could not find an existing dataset labeling attire style by decade, so I built one manually from Google images. Once this process was complete, I used transfer learning on a pre-trained deep-learning model to build my classifier.

Link to application:[DecadeStyles](http://3.14.148.127:5000/DecadeStyles)

## Data Collection
The magician's secret of machine learning is dataset quality ultimately determines the quality of results. I could not simply pull all the results from a Google search query because there was too much variation across images. I needed relatively standardized images, individuals with a full outfit with minimal background. The more variation the more challenging the learning task and ultimately more images required to increase accuracy. I built a dataset containing 100 images per category. I restricted the images to women's attire and combined decades because there were too few image results for men, and relatively few per each individual decade. To further maximize my small data pool, I augmented existing data by applying horizontal flips to images.



## Machine Learning
Since I was building an image classifier without tens of thousands of images, I used transfer learning. Transfer learning works off of a pre-trained model and replaces the final classification layers so as to apply it to a new set of images. Selecting the pre-trained model and structure of the final layers are hyperparameters to optimize on. In this case, I selected the VGG16 model with final layers of flatten, dropout (.75), and finally a dense layer (5, activation=softmax). The dropout layer was added to combat model overfitting. My selection process was manual, but advanced methods of Bayesian optimization and genetic algorithms can perform a more robust hyperparameter search. In this case, there was little marginal benefit of these methods because there was a cap on performance given the available data.

## Results
The model achieved a categorical accuracy of 73% on the validation set. Accuracy is an appropriate performance metric since the categories were balanced. For more detail see the classification report below:


![Image test]({{ site.url }}/images/Classification_Report.png)

As this report shows, the major driver of error in the model is the 1940-1950s category, which has low recall (lots of false negatives).

##Image Preprocessing 
Most of the data I am collecting is rectangular.  To fit the VGG16  mode, I need to reshape it into a 244 by 244 square matrix.  Without resizing image, this will distort the aspect ratio and potentially hurt the model's learning. Luckily, Tensorflow has an inbuilt function "tf.resize" that can resize an image without aspect distortion. It does so by shrinking the image and then padding the surrounding areas. The padding needs to be smoothed to avoid hard edges that would confound the model. This padding is computed using three possible functions, Nearest Neighbor, BIliear and Bicubic. I ran a test on these three methods and graphed the results below.

![Image test]({{ site.url }}/images/pad_result.png)
![Image test]({{ site.url }}/images/pad_labels.png)

It appears the model using the bicubic function to pad achieved a higher validation accuracy after five epochs.   Given the small size of this dataset, I  am not confident this result would be generally true, but it suggests it might be a good idea to use tf.resize when working with non-square or variable  images sizes


## Future Work
In addition to building a larger dataset, focusing on specific elements of clothing could achieve better results. For example, it would be powerful to train the machine to locate pant legs and identify bell bottoms, marking the style as 70s. With enough data, an ML algorithm would learn this on its own, but directed training achieves a better result with fewer images. Training a model to identify pant legs and collars is not trivial, and with time I can build on work that has been done in this area (see DeepFashion).

I could also further expand my data labels and training to other types of categories, like sub-cultures (hippie, punk) or levels of formality (semi-formal, business-casual) This model forms a nice base of work to expand on.


