# ERA V1 Session 5

## Create and Train a Neural Network in Python

An implementation to create and train a simple neural network in python.

## Usage
### model.py

- First we have to import all the neccessary libraries.

```ruby
import torch.nn as nn
import torch.nn.functional as F
```
- Next we build a simple Neural Network.
For this, we define a **class Net()** and pass **nn.Module** as the parameter.

```ruby
class Net(nn.Module):
```

- Create two functions inside the class to get our model ready. First is the **init()** and the second is the **forward()**.
- We need to instantiate the class for training the dataset. When we instantiate the class, the forward() function will get executed.

```ruby
def __init__(self):
    super(Net, self).__init__()
    self.conv1 = nn.Conv2d(1, 32, kernel_size=3)
    self.conv2 = nn.Conv2d(32, 64, kernel_size=3)
    self.conv3 = nn.Conv2d(64, 128, kernel_size=3)
    self.conv4 = nn.Conv2d(128, 256, kernel_size=3)
    self.fc1 = nn.Linear(4096, 50)
    self.fc2 = nn.Linear(50, 10)

def forward(self, x):
    x = F.relu(self.conv1(x), 2)
    x = F.relu(F.max_pool2d(self.conv2(x), 2)) 
    x = F.relu(self.conv3(x), 2)
    x = F.relu(F.max_pool2d(self.conv4(x), 2)) 
    x = x.view(-1, 4096)
    x = F.relu(self.fc1(x))
    x = self.fc2(x)
    return F.log_softmax(x, dim=1)
 ```
 
### utils.py
- In this we created two functions **train()** and **test()**
- train() funtion computes the prediction, traininng accuracy and loss

```ruby
def train(model, device, train_loader, optimizer, criterion):
  model.train()
  pbar = tqdm(train_loader)

  train_loss = 0
  correct = 0
  processed = 0

  for batch_idx, (data, target) in enumerate(pbar):
    data, target = data.to(device), target.to(device)
    optimizer.zero_grad()

    # Predict
    pred = model(data)

    # Calculate loss
    loss = criterion(pred, target)
    train_loss+=loss.item()

    # Backpropagation
    loss.backward()
    optimizer.step()
    
    correct += GetCorrectPredCount(pred, target)
    processed += len(data)

    pbar.set_description(desc= f'Train: Loss={loss.item():0.4f} Batch_id={batch_idx} Accuracy={100*correct/processed:0.2f}')

  train_acc.append(100*correct/processed)
  train_losses.append(train_loss/len(train_loader))
```

- And test() function calculates the loss and accuracy of the model

```ruby
def test(model, device, test_loader, criterion):
    model.eval()

    test_loss = 0
    correct = 0

    with torch.no_grad():
        for batch_idx, (data, target) in enumerate(test_loader):
            data, target = data.to(device), target.to(device)

            output = model(data)
            test_loss += criterion(output, target, reduction='sum').item()  # sum up batch loss

            correct += GetCorrectPredCount(output, target)


    test_loss /= len(test_loader.dataset)
    test_acc.append(100. * correct / len(test_loader.dataset))
    test_losses.append(test_loss)

    print('Test set: Average loss: {:.4f}, Accuracy: {}/{} ({:.2f}%)\n'.format(
        test_loss, correct, len(test_loader.dataset),
        100. * correct / len(test_loader.dataset)))
```

### S5.ipynb
- First we have to load MNIST dataset then we have to create train and test data
```ruby
from torchvision import datasets

train_data = datasets.MNIST('../data', train=True, download=True, transform=train_transforms)
test_data = datasets.MNIST('../data', train=False, download=True, transform=test_transforms)
```


- Plotting the dataset of **train_loader**


![train_loader](https://github.com/GunaKoppula/Neural-Networks/assets/61241928/e15fdb8e-f44b-4a4c-80d0-0128491ea760)


- **Training and Testing trigger**
```ruby
model = Net().to(device)
optimizer = optim.SGD(model.parameters(), lr=0.01, momentum=0.9)
scheduler = optim.lr_scheduler.StepLR(optimizer, step_size=15, gamma=0.1, verbose=True)
# New Line
criterion = F.nll_loss
num_epochs = 20

for epoch in range(1, num_epochs+1):
  print(f'Epoch {epoch}')
  utils.train(model, device, train_loader, optimizer, criterion)
  utils.test(model, device, test_loader, criterion)
  scheduler.step()
```

I used total 20 epoch
```
Adjusting learning rate of group 0 to 1.0000e-03.
Epoch 20
Train: Loss=0.0613 Batch_id=117 Accuracy=99.09: 100%|██████████| 118/118 [03:16<00:00,  1.67s/it]
Test set: Average loss: 0.0214, Accuracy: 9927/10000 (99.27%)

Adjusting learning rate of group 0 to 1.0000e-03.
```

## Training Results
![training_results](https://github.com/GunaKoppula/Neural-Networks/assets/61241928/ba302e3a-321f-4689-ad22-8e9c6051664b)



## Model Summary
![model_summary](https://github.com/GunaKoppula/Neural-Networks/assets/61241928/185d1d15-ebd8-4888-a9fd-111b751b363f)

