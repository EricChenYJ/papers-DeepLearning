# Action Recognition
參考[此網站](https://zhuanlan.zhihu.com/p/103566134)的分類，和經典論文
- 分為傳統模型與基於深度學習的方法。
- 其中，基於深度學習的方法，可以分為 skeleton-based 和 video-based 兩類。

![](https://i.imgur.com/O1cGKbk.png)



# 靜態圖片方法
1、IDT

2、2-stream

3、IDT + 2-stream

4、LSTM + 2-stream

:::info
從SIFT特徵轉CNN特徵
:::

# Skeleton-based
代表性算法
- ST-NBNN
- Deformable Pose Traversal Convolution
- ST-GCN

# Video-based

## TwoStream (RGB+Optical flow)
融合了RGB 提取的**空間特徵**和光流分支所提取的**時間上的光流特徵**，進行行為識別。

代表性方法
- TwoStreamCNN
- TSN
- TRN

## 3D Conv
將2D捲積擴展到3D，直接提取時間和空間的特徵。

代表性方法
- C3D
- P3D
- R(2+1)D
- ECO
- SlowFast (不太算TwoStream -> Two Pathway)
- MARS


## CNN-LSTM
使用CNN提取空間特徵，使用RNN提取時序特徵，進行行為識別。

代表方法
- LRCN


---
1、Deep Temporal Linear Encoding Networks
- two-stream ConvNets的補充與改進

2、Spatiotemporal Multiplier Networks for Video Action Recognition

3、Spatiotemporal Pyramid Network for Video Action Recognition

4、Spatio-Temporal Vector of Locally Max Pooled Features for Action Recognition in Videos

5、Asynchronous Temporal Fields for Action Recognition

6、AdaScan:Adaptive Scan Pooling in Deep Convolutional Neural Networks for Human Action Recognition in Videos
