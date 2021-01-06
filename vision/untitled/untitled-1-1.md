---
description: CNN
---

# Convolution Neural Network

## CNN

### import

```
import torch
import torchvision
import torchvision.transforms as transforms
from torch.utils.data import DataLoader
```

### Load data set

```python
transforms = transforms.Compose(
    [transforms.ToTensor(),
    transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))])

trainset = torchvision.datasets.CIFAR10(root='/pytorch/data1', train=True,
                                        download=True, transform=transforms)
trainloader = torch.utils.data.DataLoader(trainset, batch_size=8,
                                          shuffle=True, num_workers=0)
testset = torchvision.datasets.CIFAR10(root='/pytorch/data1', train=False,
                                        download=True, transform=transforms)
testloader = torch.utils.data.DataLoader(testset, batch_size=8, 
                                        shuffle=True, num_workers=0)
classes = ('plane', 'car', 'bird', 'cat', 
            'deer', 'dog', 'frog', 'horse', 'ship', 'truck')
```

### Build a model

```python
import torch.nn as nn
import torch.nn.functional as F 


class Net(nn.Module): # nn.Module 상속 받음 
    # 연산을 처음에 세팅해 주는 부분 
    def __init__(self):
        super(Net, self).__init__()
        ## 여기까진 그대로 작성 
        self.conv1 = nn.Conv2d(3,6,5)
        self.pool = nn.MaxPool2d(2,2)
        self.conv2 = nn.Conv2d(6, 16, 5)
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    # init에서 정의된 걸 가지고 여기서 사용 (연산의 순서 정해주는 부분)
    def forward(self, x):
        x = self.pool(F.relu(self.conv1(x)))
        x = self.pool(F.relu(self.conv2(x)))
        x = x.view(-1, 16*5*5) # size 변형할 때 많이 쓰임, fully conntected는 사각형인데 그것을 일자로 펴기 위한 작업 
        x = F.relu(self.fc1(x)) # Linear 연산하고, 렐루 넣고 
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x 

# 클래스에 대한 인스턴스 생성 
net = Net()

print(net)
```

### Implement the model with training set

```python
import torch.optim as optim

criterion = nn.CrossEntropyLoss() # loss function 
optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9) # gradients 구하는 변수 
# net 안에 있는 파라미터들을 모아놓은 것, 그것을 업데이트 한다. Ir 보폭 수에 해당하는 숫자 
# momentnum 

for epoch in range(10): # loop over the dataset multiple times
    running_loss = 0.0
    for i, data in enumerate(trainloader, 0):
        # get the inputs; data is a list of [inputs, labels]
        inputs, labels = data # image data, label data 

        #zero the parameter gradients
        optimizer.zero_grad() # optimizer 초기화 

        #forward + backward + optimize
        outputs = net(inputs) # image model net에 input을 넣어주는 것 
        loss = criterion(outputs, labels) # cross entropy로 loss 계산 
        loss.backward() # 역전파 
        optimizer.step()  # 옵티마이저 계산 

        # print statistics
        running_loss += loss.item() # loss 는 스칼라 값, item은 숫자 하나의 형태가 됨  
        if i % 2000 == 1999:
            print('[%d, %5d] loss: %.3f' % (epoch + 1, i+1, running_loss / 2000))
        running_loss = 0.0

print('Finished Training')
```

### Save the trained model

```python
# 학습 모델 저장 
PATH = './cifar_net.path'
torch.save(net.state_dict(), PATH)

### Load the pre-trained model
# 불러오기 
net = Net() 
net.load_state_dict(torch.load(PATH))

correct = 0 
total = 0 
# test data는 gradient 필요없으니까 no_grad()
with torch.no_grad(): # 추적을 안 한다는 말 
    for data in testloader: # testloader를 배치로 나누었기 때문에 for문 씀 
        image, labes = data
        outputs = net(image)
        _, predicted = torch.max(outputs.data, 1) # 가장 큰 자리값을 구한다는 것 
        total += labels.size(0)
        correct += (predicted == labels).sum().item() 
print('Accuracy of the network on the 10000 test images: %d %% ' % (
    100 * correct /total))
```



