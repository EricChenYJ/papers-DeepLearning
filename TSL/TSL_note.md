# Intorduction
###### `Pose estimation`,  `Hand recognition`,  `Action recognition`,  `Video classify`
- 提出利用單一手語視訊，建構深度學習訓練資料的方法
- 透過mask rcnn 找出所有手部和臉部的遮罩
- 在透過空間域data augmentation創建不同內容的資料集
- 採用時間域採樣策略，模擬手語老師手速
- 用3D-ResNet with attention對手語辭彙進行分類


# Annotation type

0. bbox
1. segment
2. skeleton

- used for human posture evaluation, gesture recognition
- computation cost is the most

# Dataset
## TSL
1、convert frames from RGB to HSV and selected word region masks
- dilated word's mask

2、`use HSV and flood fill select skin region then use mask rcnn  to get hands segmentation mask`
## Data augmentation
[convert img size](https://github.com/jgm/pandoc/issues/2554)
<img src="https://user-images.githubusercontent.com/48618187/137102958-e6e550e9-f6ca-4296-808a-b815cb4a1e8f.png" width="500">


- produce in training phase
### spatial domaain
1、Resize to 224x224

2、To simulate skin color
- brightness to range [0.2, 2]
- rotating signer +15/-15 degree randomly
- shift randomly +-0.0105x and +-0.00622y and propotional scale to [0.6, 1.5] times.
- randomly select background set
- normalize data to mean:0.5, standard deviation:0.5
### temporal domain

1、Stastisic the average frames first (about 50 frames)
2、一旦我們決定的幀數超過40幀，我們就放棄其中的一部分，直到選擇到40幀； 如果它們小於 40，我們隨機重複一些幀，使選擇為 40。

- random select : 隨機選擇40幀，並限制其第一幀和最後一幀的距離大於或等於{30, 35, 40, 45, 50}
- center select 
- begin select
- average select

# Archcitecture
<img src="https://user-images.githubusercontent.com/48618187/137105269-dc723085-731f-4f98-82ba-eb70760c374d.png" width="500">
3D-ResNet
SENet
Attention

# Training
TrainSet: 753 classes to 23k videos
input: `image sequence` of 224 pixels × 224 pixels × 3 channel × 40 frames
Optimizer: `Ranger`
Loss function: `Cross-entropy`
Learning-Rate: `1e-5`
Batch size: 4
Epoch: 100
## type
![image](https://user-images.githubusercontent.com/48618187/137114155-f0856c6f-a38e-441c-9d09-ed0909b0b478.png)




# Testing
TestSet: 一樣的手語老師，種類較少的手語視訊
Temporal: random select 40 frames
Spatial: nomalization and resize to 224x224

# Experiment
如果我們使用整個合成數據或原始數據作為訓練數據集，性能會很低。最終的訓練數據集由`原始數據和合成數據`組成。
![image](https://user-images.githubusercontent.com/48618187/137114399-27926155-6504-4fe0-aeaf-62617d1ef6c3.png)
## Spatial domain result
![image](https://user-images.githubusercontent.com/48618187/137114560-b466ccee-f3be-4af6-9632-dab7fa68bd25.png)
剪下手，貼到不同的背景上，混合一些數據，將手語老師踢到不同的背景，提升效能
## Temporal domain result
![image](https://user-images.githubusercontent.com/48618187/137114597-1233d663-1c6e-4a32-b168-472f3b75b520.png)

## Attention effect
![image](https://user-images.githubusercontent.com/48618187/137118516-e52b57ac-60d3-4491-bc4a-4aa146858b53.png)
加入SENet 的 channel attention 
