# Deep Visual Sun Sensing
## Project Description:
This repository attempts to predict the azimuth of the sun from 2D RGB images. Predicting the angle of the sun is helpful in robotic applications because it allows robots to localize. Higher end robots require sensors that are dedicated to tracking the location of the sun. Being able to predict the exact azimuth of the sun from 2D images reduces the number of sensors required on modern robots.

## Classification Architecture:
 To reduce problem complexity, the problem is formulated as a classification problem as opposed to a 3D vector orientation regression problem. The model takes in RGB images of size 68 x 224 and classifies these images into 18 bins. Each bin represents a 20 degree angle spread. 

 ## Model Architecture:
 Several models were attempted prior to achieving acceptable accuracy. The most successful model utilized 3 convolutional-pooling layers followed by 3 fully connected layers. The model architecture is detailed in the code exercpt below:

 ```py
class CNN(torch.nn.Module):
    def __init__(self, num_bins):
        super(CNN, self).__init__()

        self.conv1 = torch.nn.Conv2d(3, 6, kernel_size=3)
        self.conv2 = torch.nn.Conv2d(6, 12, kernel_size=5)
        self.conv3 = torch.nn.Conv2d(12, 14, kernel_size=7)
        self.pool = torch.nn.MaxPool2d(kernel_size=2, stride=2)
        hidden_layer_size = 128
        hidden_layer_2_size = 64
        self.fc1 = torch.nn.Linear(14 * 4 * 23, hidden_layer_size)
        self.fc2 = torch.nn.Linear(hidden_layer_size, hidden_layer_2_size)
        self.fc3 = torch.nn.Linear(hidden_layer_2_size, num_bins)
        self.relu = torch.nn.ReLU()


    def forward(self, x):
        x = self.pool(self.relu(self.conv1(x)))
        x = self.pool(self.relu(self.conv2(x)))
        x = self.pool(self.relu(self.conv3(x)))
        x = x.view(-1, 14 * 4 * 23)
        x = self.relu(self.fc1(x))
        x = self.relu(self.fc2(x))
        x = self.fc3(x)

        return x
 ```
 Kernel sizes increased towards the tail of the network. The same pooling layer with a stride of 2 was used for each of the convolutional layers.

 ## Results:
 Using 45 degree bins the model achieved a test accuracy upwards of 84%. Using 20 degree bins the test accuracy was approximately 74%. 

 ### Project Cloning Instructions:
 In order to reproduce this project the KITTI dataset used to run the neccessary files is zipped into the following drive folder:

https://drive.google.com/drive/folders/1Y2QYtFxUBWJWifCtsMeI3Urfnr0A885N?usp=sharing

Instructions:
1. Run sun_cnn_20.py
2. Run test_sun_cnn_20.py
3. Predictions are outputed automatically into predictions_20.txt and a summary of the training results is included in the autogenerated file net-train.pdf
