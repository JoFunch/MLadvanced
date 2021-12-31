# MLadvanced

1. Data prep.
The images are drawn from the json-training list. initially, they are put into a dictionary where images are keys and all the polygons are values. Previously, I had the same image for every item in the polygon, but that was enormous processing and had to be changed, as I figured these coud indeed be concatenated. It would be interesting to see the trade-off between the two approaches. I only kept those polygons where the bool was true ( as there were also ones which were ignored for being too poor quality). For performance, I made a counter stopping and returning the extracted items 100 items. This is solely a performance thing. it could easily be scaled to more or full. 

Then, boxes are made. This is done for all bounding boxes PER image in iteration. Practically, a grid is being made as a canvas with the same size as the images consisting of 0s. The polygon is then placed on the grid and turning its area into 1's. The grid is continusously updated with however many polygons for that one image and then placed in a list to-be X's and Y's (input and labels). 

The images are normalised by a simple divide-by-pixel as convolutional neural networks (any machine learning operation) is better off given smaller numbers rather than large as it confuses the engine. 

Following the normalisation, test, train and validation sets are made.

2. Models.

The first model is fully connected model with 2 convolutional layers and three linear layers. The convolutional layers are also each respectively followed by a RelU activation functions and Max pooling layers. The model finishes through a sigmoid function for its non-linearity. The output layer of the last linear layer is 64, which means that, as we want to estimate the chance of each pixel being a part of chinese characters, it would have to be expanded /upscaled into the image size to match the picture. Initially, in my model, the output-layer of the model was indeed the same as the image-size, as I believed it would be simpler. this turned out not to really to work, as the output would generally be too imprecise.

I trained this model with a BCE (Binary Cross Entropy-loss function) as the initial task was indeed binary.
I trained this model for 10 epochs, Which seemed to be a fine spot, but could easily have been decreased to half. 
The optimiser used is Stochastic gradient descent for its computational advantages. SGD updates faster and does not require as much computational power for its updates (even though it seems relative for this task). 

as mentioned, during training, in order to compute loss, the images were upscaled to match the array-sizes of the labels (full image in this case. I didnt downscale which, retrospectively, was a mistake). This upscaling was doing both during the training, testing and predicting since it was the only way to get a pixel-for-pixel estimation.

model 1 Loss:
- Epoch [1/10], Loss: 0.5863032937049866
- Epoch [2/10], Loss: 0.21119970083236694
- Epoch [3/10], Loss: 0.048833709210157394
- Epoch [4/10], Loss: 0.02527598664164543
- Epoch [5/10], Loss: 0.04726000502705574
- Epoch [6/10], Loss: 0.030520491302013397
- Epoch [7/10], Loss: 0.019646162167191505
- Epoch [8/10], Loss: 0.02794039621949196
- Epoch [9/10], Loss: 0.009800591506063938
- Epoch [10/10], Loss: 0.024047497659921646



The second model had more weight on the convolutional layers to see what the difference might be opposing a focus in model 1 on the linear layers. Instead, the second model had only one finishing linear layer but 4 convolutional layers. For the second model, a dropout layer was also introduced follow the convolutional part of the model. 


model 2 Loss:
- Epoch [1/5], Loss: 0.286181777715683
- Epoch [2/5], Loss: 0.24633407592773438
- Epoch [3/5], Loss: 0.18563270568847656
- Epoch [4/5], Loss: 0.27632713317871094
- Epoch [5/5], Loss: 0.3473043441772461



3. Eval and Pred

model 1 Testing:
I am also running a loss-function during the testing. I am doing so simply to make sure that the model doesnt memorize training data, and to see that it acts regularly on the data. The model will not take any affect of the calculated loss because the test-data does not backpropagate the data and forward the learning to the next iteration.

It was my ambition and intent to try and see how the Loss here would perform. It is obviously not something which is required (strictly speaking) in the testing phase, but since i experience error in my testing phase, I thought it could share light on the issue.

Model 1 Loss
- Round of loss:  0.08400718122720718
- Round of loss:  0.04109744355082512
- Round of loss:  0.016097912564873695
- Round of loss:  0.015201829373836517
Model 1 Acc
- Accuracy: 0.33

Model 2 Loss
Loss:  0.07238388061523438
Model 2 Acc
Accuracy: 0.17



Following the eval of both models, a predicted is attmpted, however despite priting/loading/accepting items as intended, somewhere in the algorithm of the CNN, mistakes were made such that the image as well as the predicted pixels are flawed. Neither the original image nor the predicted pixel area is of anything which resembles "meaning". 



