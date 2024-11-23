# Picture-Sorting
This project consits of identifying whether a picture had been taken indoors or outdoors from a Columbia Picture dataset.


This was part of a greater project comparing the effectiveness of a Logistic Regression against a machine learning method of our choice. I decided to experiment utilizing multiple different Neural Network methods. These methods vary in both the Layering/Neuron methods as well as the partinining methods of the RGB pixel panels. The Layering and number of neurons were based on the three rules of thumbs develpoed by J. Heaton in his Introduction to Neural Networks 2nd Edition (2008). 


Although the final conclusions are not available in this repository, even with the implementation of the various Neural Networks methodologies, the Logisitc Regression demonstrated slighlty better AUC against the best Neural Network methodology, with the added benefit of being much less computationally costly.  


NOTE: The Logisitc Regression method in which I compared the Neural network method utilized the median pixels of each RGB panel and was subsequently trained on these three features only. 
