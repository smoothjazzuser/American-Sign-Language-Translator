**Abstract:** 
The objective of this project was to create an Android app that can be used as an American Sign Language translator, specifically for the letters of the alphabet. The app works by capturing an image of someone hand gesturing a letter, and then identifies and displays which sign is being shown. The purpose of creating the application was to allow utilization for educational purposes, or even as a potential way to communicate in the real world. Although the scope of the project only includes stationary gestures, and is limited to the letters of the alphabet, it has potential to be further developed into a much more enhanced version, including real-time readings with words and sentences.  
The program was implemented by integrating a convolutional neural network to train a model using a wide range of datasets containing signs of the alphabet in different environments. Further enhancements were made with a substantial amount of retraining, validating, and incorporating data augmentation to strengthen the model and increase its accuracy. Finally, the model was made into a mobile app with the use of Android Studio and Tensorflow Lite, allowing the user to input an image and see the displayed results.  
 
**Getting started:**
1) git clone https://github.com/smoothjazzuser/American-Sign-Language-Translator/
2) Search kaggle for an appropriate ASL gesture dataset. See bellow for the ones we explored. Diverse datasets generalize far better than ones with all the same types of images. 
3) Edit asl_basic_learner so that datapath and other variables in the beginning section refer to where your files are kept. There should be a path named "unsorted" that will be devided into training and validation data within the script. Optionally, you can enable canny edge detection here by uncommenting the function call within abstraact(). There should also be a folder in the base directory called "lite" that stores TFLite models.
4) Run asl_basic_learner to train the model, and optionally tune hyperperameters. RGB images could benifit from experimenting with brighntess and contrast data augmentation layers in Keras. See https://www.tensorflow.org/tutorials/images/data_augmentation
5) The resulting .tflite model requires inputs of size 200x200 and a depth of 3 (unless you choose color_mode = 'grayscale' images in asl_basic_learner). It outputs a tensor with 28 numbers between 0 and 1 representing the models confidence that it is each class. The order from left top to bottom right is A-Z are 0-25 and 'nothing' and 'space' are 26 and 27. 
6) We have uploaded a pdf with the general code to add this model to an app. The follwoing are useful sites to further explore a more guided integration:

https://github.com/android/camera-samples/tree/main/CameraXTfLite

https://www.tensorflow.org/lite/guide

https://developer.android.com/training/basics/firstapp

https://developer.android.com/guide/components/fundamentals

https://www.geeksforgeeks.org/android-how-to-open-camera-through-intent-and-display-captured-image/.

**Datasets:**

https://www.kaggle.com/ammarnassanalhajali/american-sign-language-letters

https://www.kaggle.com/grassknoted/asl-alphabet

https://www.kaggle.com/belalelwikel/asl-and-some-words

https://www.kaggle.com/allexmendes/asl-alphabet-synthetic

https://github.com/ruslan-kl/asl_recognition.

**Pretrained Models:**

RGB, 93.6% accuracy: 
https://drive.google.com/file/d/1DQRqfSJ5crlpOtzTxPqEf4w570_05pHm/view?usp=sharing

Grayscale, 87% accuracy: 
https://drive.google.com/file/d/1zecVFTAOjZ4zCZarGU930hdcdyWLovus/view?usp=sharing

Canny edge 85% validation accuracy: 
https://drive.google.com/file/d/1x1HAVW69q0U7x7-kx1dFEa2Dubnysr0T/view?usp=sharing
 
**Data specification:**

The initial training dataset was designed for supervised learning for image classification with labels and was composed of a combination of two sets found on Kaggle (Akash and Dan Rasband [reference]). The images were cropped to show primarily show gestures. This combined dataset consisted of 28 categories: one for each letter of the alphabet, A-Z, as well as a ???space??? character and a ???nothing??? label. Each category had 3,030 color images at 200x200 resolution. However, it was significantly lacking in diversity and was not generalizable. A second attempt to improve the database yielded 4626 images per training category, and 402 per validation category. Overall, the data available online seemed very non-generalizable with minor variation within but great variation between datasets. This led to very poor model convergence until we pooled a much more diverse dataset from various sources. Yet this still could pose significant challenges in how well the model will perform in highly varied settings. This new dataset nevertheless contained significantly more varied backgrounds than the previous.  

**Design and Milestones:** 

The backbone of the model training used Jupyter and Keras, which is now part of the TensorFlow library. The model architecture consisted of a sequential model containing augmentation layers to flip, rotate, rescale, etc. OpenCV provided the canny algorithm for edge detection. However, our results using this were suboptimal because each threshold and gamma setting only worked for specific picture environments. Noisy versus simple backgrounds did not work under the same settings, which significantly harmed our ability to use this function. The loaded training images, convolutional and maxpooling2d layers, and then a final three layers of Dense neurons. The swish activation function proved to converge better than relu when compared during training, and a SpacialDropout2D layer instead of a Dropout layer between the second and third set of convolution layers improved performance by 15%. While initially using grayscale, canny edge detected images in our database, we decided for practical purposes that we did not have enough time to implement that into the app. However, during training we found that canny edge detection did improve performance when properly tuned. However, grayscale significantly decreased the performance of non-edge-detected images. Switching between canny and non-edge detected databases also necessitated re-tuning all hyperparameters.  
Utilization of the TensorFlow Lite model in a mobile environment was accomplished using Android Studio and publicly available online tutorials and example projects. Due to difficulty in training, the app development process was significantly delayed. The CameraX libraries and inherent TFLite integration supported in Android Studio made it the ideal environment for that portion of the project. For future projects however, it is advisable to know what input dimensions and depth to include in the TensorFlow models since that cannot be changed after training without defining custom functions which do not work well with TFLite. 


**Results and Analysis:** 

Over the course of tuning and retraining the model, the highest accuracy of 94% used non edge-detected RGB images. While we expected canny edge detection to improve the generalizability of our model, it did not do so in practice when implemented. Initially, we found very good validation accuracy in our first dataset. However, this was primarily because of contamination between various datasets on Kaggle having extremely similar pictures. The model was thus reporting a higher score than it would get in the real world. Taking a different validation dataset plummeted our validation accuracy to 8% while the training dataset was reaching perfect accuracy. In order to deal with the difficulty in finding a good dataset, we collected several datasets together then split them into training and validation. While this may still have multiple similar images, it should provide a more realistic estimation of model accuracy in the real world than many projects on highly selective datasets that would not work elsewhere. Previous attempts with the dataset resulted in 85% accuracy for canny using dropout and grayscale, 87% accuracy for grayscale with no canny detection. And finally, our above result of 94% accuracy once dropout was replaced with the spatial variant, SpacialDropout2D and RGB. For future projects that want to improve our methods by adding edge detection, training a neural network to clean the data into clear edges beforehand might be a great way to solve the problem of threshold for canny and too much noise. Additionally, we predict that some letters that have motion in them will have significant problems in the real-world. Since we are limited to still photos, most of the datasets tried to deal with this by taking the photos differently when spatial awareness of movement was required. However, this would significantly complicate actual applications in the real world. Z was confused with X some of the time, several letters were confused with I to a moderate degree, O performed markedly worse than the average, and many other letters were confused for Y. However, the other letters seemed less likely to be confused for each other than the above listed. However, these results do not necessarily apply in the real world, depending on the accuracy of the datasets, which overall seemed poorly constructed from a generalizability standpoint.  
