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

## code factory

### main.py

```python
import argparse #
import training # 
import datasets #file 에서 만들어진 데이터 
from tsne import tsne #tsne 불러오는 함수 

if __name__ == "__main__":

    parser = argparse.ArgumentParser(description='CIFAR10 image classification')
    # parser 이용 방법 
    # terminal에서 해당 변수들을 다 컨트롤 할 수 있음 
    parser.add_argument('--batch_size', default=128, type=int, help='batch size')
    parser.add_argument('--epoch', default=101, type=int, help='training epoch')
    parser.add_argument('--lr', default=1e-3, type=float, help='learning rate')
    parser.add_argument('--l2', default=1e-4, type=float, help='weight decay')
    parser.add_argument('--model_name', default='resnet18', type=str, help='model name')
    parser.add_argument('--pretrained', default=None, type=str, help='model path')
    parser.add_argument('--train', default='train', type=str, help='train and eval')
    args = parser.parse_args()
    print(args)

    # 데이터 불러오기
    # 
    trainloader, testloader = datasets.dataloader(args.batch_size)
    print('Completed loading your datasets.')

    # 모델 불러오기 및 학습하기
    learning = training.SupervisedLearning(trainloader, testloader, args.model_name, args.pretrained)

    if args.train == 'train':
        learning.train(args.epoch, args.lr, args.l2)
    else:
        train_acc = learning.eval(trainloader)
        test_acc = learning.eval(testloader)
        print(f' Train Accuracy: {train_acc}, Test Accuraccy: {test_acc}')

        # t-SNE graph
        tsne(testloader, args.model_name, args.pretrained)


```

### datasets.py

```python
import torchvision
import torchvision.transforms as tr
from torch.utils.data import DataLoader

# Data loader
def dataloader(batch_size):

    # 전처리 정의, 일반적으로 Noramlize를 처리하면 빨라짐 
    transf = tr.Compose([tr.RandomCrop(32, padding=4), tr.RandomHorizontalFlip(), tr.ToTensor(),
                          tr.Normalize((0.4914, 0.4822, 0.4465), (0.2023, 0.1994, 0.2010))])

    test_transf = tr.Compose([tr.ToTensor(), tr.Normalize((0.4914, 0.4822, 0.4465), (0.2023, 0.1994, 0.2010))])

    trainset = torchvision.datasets.CIFAR10(root='./data', download=True, train=True, transform=transf)
    trainloader = DataLoader(trainset, batch_size=batch_size, num_workers=0)

    testset = torchvision.datasets.CIFAR10(root='./data', download=True, train=False, transform=test_transf)
    testloader = DataLoader(testset, batch_size=batch_size, shuffle=False, num_workers=0)

    return trainloader, testloader
```

### models.py

```python
import torch
import torch.nn as nn
import torch.nn.functional as F

# 역전파할 때 전정보 
class ResidualBlock(nn.Module):

    def __init__(self, in_planes, planes, stride=1):
        super(ResidualBlock, self).__init__()
        
        self.conv_block = nn.Sequential(
            nn.Conv2d(in_planes, planes, kernel_size=3, stride=stride, padding=1, bias=False),
            nn.BatchNorm2d(planes),
            nn.ReLU(),
            nn.Conv2d(planes, planes, kernel_size=3, stride=1, padding=1, bias=False),
            nn.BatchNorm2d(planes)
        )

        self.downsample = nn.Sequential()
        if stride != 1 or in_planes != planes:
            self.downsample = nn.Sequential(
                            nn.Conv2d(in_planes, planes, kernel_size=1, stride=stride, bias=False),
                            nn.BatchNorm2d(planes)
                            )
    #skip connection 
    def forward(self, x):
        out = self.conv_block(x)
        out += self.downsample(x)

        out = F.relu(out)
        return out
# output size = 1 
# input size = 

class ResNet(nn.Module):
    def __init__(self, block, num_blocks, num_classes=10):
        super(ResNet, self).__init__()
        self.in_planes = 64

        self.base = nn.Sequential(
                        nn.Conv2d(3, 64, kernel_size=3,stride=1, padding=1, bias=False),
                        nn.BatchNorm2d(64),
                        nn.ReLU()
                        )
        self.layer1 = self._make_layer(block, 64, num_blocks[0], stride=1)
        self.layer2 = self._make_layer(block, 128, num_blocks[1], stride=2)
        self.layer3 = self._make_layer(block, 256, num_blocks[2], stride=2)
        self.layer4 = self._make_layer(block, 512, num_blocks[3], stride=2)
        self.gap = nn.AvgPool2d(4)
        self.fc = nn.Linear(512, num_classes)

    def _make_layer(self, block, planes, num_blocks, stride):
        strides = [stride] + [1]*(num_blocks-1)
        layers = []
        for stride in strides:
            layers.append(block(self.in_planes, planes, stride))
            self.in_planes = planes
        return nn.Sequential(*layers)

    def forward(self, x):
        out = self.base(x)
        out = self.layer1(out)
        out = self.layer2(out)
        out = self.layer3(out)
        out = self.layer4(out)
        out = self.gap(out)
        out = out.view(out.size(0), -1)
        out = self.fc(out)
        return out

def modeltype(model):
    if model == 'resnet18':
        return ResNet(ResidualBlock, [2, 2, 2, 2])

    elif model == 'resnet34':
        return ResNet(ResidualBlock, [3, 4, 6, 3])


```

### training.py

```python
import torch
import torch.nn as nn
import torch.optim as optim
import models
from matplotlib import pyplot as plt
from tqdm import tqdm 

class SupervisedLearning():

    def __init__(self, trainloader, testloader, model_name, pretrained):

        self.device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
        self.trainloader = trainloader
        self.testloader = testloader

        self.model_name = model_name
        self.model = models.modeltype(self.model_name)
        self.model = self.model.to(self.device)

        if pretrained != None:
            self.model.load_state_dict(torch.load(pretrained))
            print('Completed you pretrained model.')

        print('Completed loading your network.')

        self.criterion = nn.CrossEntropyLoss()

    def eval(self, dataloader):

        correct = 0
        total = 0
        self.model.eval()

        with torch.no_grad():
            for data in dataloader:
                images, labels = data[0].to(self.device), data[1].to(self.device)
                outputs = self.model(images)
                _, predicted = torch.max(outputs.data, 1)
                total += labels.size(0)
                correct += (predicted == labels).sum().item()

        acc = 100 * correct / total

        return acc

    def train(self, epoch, lr, l2):

        optimizer = optim.Adam(self.model.parameters(), lr=lr, weight_decay=l2)

        train_loss_list = []
        test_loss_list = []
        n = len(self.trainloader)
        m = len(self.testloader)
        test_loss = 10 # dummy

        print("Start training the model.")
        for epoch in tqdm(range(epoch)):

            running_loss = 0.0

            for data in self.trainloader:

                inputs, labels = data[0].to(self.device), data[1].to(self.device)
                optimizer.zero_grad()
                outputs = self.model(inputs)
                trainloss = self.criterion(outputs, labels)

                trainloss.backward()
                optimizer.step()

                running_loss += trainloss.item()

            train_cost = running_loss / n
            train_loss_list.append(train_cost)

            running_loss = 0.0

            for data in self.testloader:

                inputs, labels = data[0].to(self.device), data[1].to(self.device)
                outputs = self.model(inputs)
                testloss = self.criterion(outputs, labels)
                running_loss += testloss.item()

            test_cost = running_loss / m
            test_loss_list.append(test_cost)

            if epoch % 10 == 0:
                print(f'Epoch {epoch}, Train Loss: {train_cost}, Test Loss: {test_cost}')

            if test_cost <= test_loss:
                torch.save(self.model.state_dict(), './results/' + self.model_name  + '_best.pth')
                test_loss = test_cost
                best_epoch = epoch

        torch.save(self.model.state_dict(), './results/' + self.model_name + '_last.pth')
        print('Finished Training')

        # Graph
        plt.figure(figsize=(8, 5))
        plt.plot(train_loss_list)
        plt.plot(test_loss_list)
        plt.legend(['Train Loss','Test Loss'])
        plt.savefig('./results/'+ self.model_name +'_graph.png')

        self.model.load_state_dict(torch.load('./results/' + self.model_name  + '_best.pth'))
        train_acc = eval(self.trainloader)
        test_acc = eval(self.testloader)
        print(f'Epoch{best_epoch}: Train Accuracy: {train_acc}, Test Accuraccy: {test_acc}')


```

### tsne.py

```python
from sklearn.manifold import TSNE
import numpy as np
from matplotlib import pyplot as plt
import models
import torch
import torch.nn as nn

class Identity(nn.Module):
    def __init__(self):
        super(Identity, self).__init__()

    def forward(self, x):
        return x

def tsne(dataloader, model_name, pretrained):

    device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
    model_name = model_name
    model = models.modeltype(model_name)
    model = model.to(device)
    model.load_state_dict(torch.load(pretrained))
    model.fc = Identity()

    actual = []
    deep_features = []

    model.eval()
    with torch.no_grad():
        for data in dataloader:
            images, labels = data[0].to(device), data[1].to(device)
            features = model(images)

            deep_features += features.cpu().numpy().tolist()
            actual += labels.cpu().numpy().tolist()

    tSNE = TSNE(n_components=2, random_state=0)
    cluster = np.array(tSNE.fit_transform(np.array(deep_features)))
    actual = np.array(actual)

    plt.figure(figsize=(10, 10))
    cifar = ['plane', 'car', 'bird', 'cat', 'deer', 'dog', 'frog', 'horse', 'ship', 'truck']
    for i, label in zip(range(10), cifar):
        idx = np.where(actual == i)
        plt.scatter(cluster[idx, 0], cluster[idx, 1], marker='.', label=label)

    plt.legend()
    plt.savefig('./results/'+ model_name+'_tSNE.png')

```



