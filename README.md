# Multi-Class Classication of network protocols using Neural Network and XGBoost


****The steps below are neatly presented in the notebook  

Corrupt values
After studying the dataset using ds.describe(), we can notice that it consists of many NaN records and inf records. These records are corrupt and might affect the accuracy of the model to produce unexpected results. So, they have to be eliminated.

Shuffling
When data is collected or organized in a specific order, the order itself might contain patterns or biases that are not representative of the underlying data distribution. Shuffling the data helps remove this bias by ensuring that each data point has an equal chance of being selected during training or validation.

Feature Selection:
After analysing each feature, I chose the features which were consistent, and had a measurable impact on classifying the label. The features were :
['Protocol', 'Fwd Packet Length Max','Fwd Packet Length Min','Bwd Packet Length Mean', 'SYN Flag Count', 'Flow Duration', 'Total Fwd Packets', 'Total Backward Packets', 'Flow Packets/s', 'Flow Bytes/s', 'RST Flag Count', 'Inbound']

The label which we have to classify consists of 10 classes. I mapped each class with a number from 0 to 9, in order to give input to the ML model.
- {'BENIGN': 0, 'DrDoS_NTP': 1, 'DrDoS_NetBIOS': 2, 'UDP-lag': 3, 'DrDoS_LDAP': 4, 'DrDoS_SSDP': 5, 'DrDoS_UDP': 6, 'Syn': 7, 'DrDoS_MSSQL': 8, 'WebDDoS': 9}

- Normalization
There are features in the dataset which have widely varying range of values. In order to make the process of gradient descent regular and more efficient, we have to normalize the data. It is essential when we are dealing with multiple features. I chose Z-score normalization

Label encoding :
The feature "Protocol" has 3 categories 0, 6 and 17. I used a dictionary to map the categories to the values 0,1,2 so that they can be one hot encoded.
{0.0: 0, 17.0: 1, 6.0: 2}

Splitting the dataset
In order to determine how good a model is at predicting (generalizing), it is essential to test it on unseen data which it has not been trained on before.

It will be difficult to assess if the model is overfitted. So, we can split the dataset into training_data and test data in a ratio 8:2.

IMPORTANT : Split the data randomly after shuffling to prevent incosistencies

Handling Categorical Features:
While handling categorical features, we can't use 0 and 1 directly, as 1 doesn't indicate a higher value than 0. So we can use one hot vectors to represent 0 and 1.

[1 , 0] represents 0
[0 , 1] represents 1

Neural Network:
I am using 12 keras input layers, each for a different feature.

The input layer instantiates a tensor which consists of all the values in the column of the specific feature.

A tensor is similar to a numpy array, but tensorflow deals with tensors instead of lists or numpy arrays.

Architecture
First layer consists of 32 neurons with relu activation.

Second layer consists of 20 neurons with relu activation.

Third layer consists of 10 neurons with softmax activation.

I chose 10 neurons for third layer because there are 10 classes for label. Each neuron of the last layer will output a value between 0 and 1. The neuron with the highest output will be predicted.

Configuring the model
Before training the model, we configure it using model.compile. Here, we specify optimizer, loss and metrics to assess the model's performance.

Adams optimzer is a standard optimzer that changes the learning rate accordingly to the rate of gradient descent.

Sparse_categorical_crossentropy is used when classifying multiple classes with the help of softmax activation function. Since the label is in the form of categories and not one hot encoded, we are using sparse.

Accuracy is the ratio of the number of times the model gives a correct prediction to the total number of predictions.

Training the model
The neural network is trained on the data for 5 epochs. i.e it minimizes loss by iterating through all the the training examples exactly 5 times

Since we are dealing with a very large dataset, it is better if we divide each epoch into batches of fixed size. This will lead to faster convergence of loss and smoother gradients.

Evaluating the model.
We can test the model on test data and compare the predictions of the label to the true values of the table to determine accuracy. The accuracy of this model is 0.7584146857261658.

The proportion of actual positives that are identified correctly is given by recall , where as the proportion of positive identifications that was actually correct is precision.

We can adjust the classification threshold to increase or decrease recall and precision.
Both are in a tug of war, increase in precision leads to decrease in recall and viceversa.
Decreasing the value of classification threshold can help in increasing recall for a particular class.
So it is best to consider f-score as it is the mean of precision and recall.
