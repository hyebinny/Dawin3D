
<div align="center">
<h1> Dawin3D </h1>
<h3> Dynamic Window Transformer for 3D Indoor Scene Understanding </h3>

<sup>1</sup> Hyebin Kim ,
<sup>2</sup> Sangmin Yoon,
<sup>1</sup> Jungho Yoon

<sup>1</sup>  Ewha Womans University  
<sup>2</sup>  Kookmin University

📝 Paper: (paper link)

</div>

![Teaser](./asset/Dawin3D_teaser.jpg)

## Abstract

3D semantic segmentation is a dense prediction task that assigns a semantic category to every point in a 3D scene.  

The unordered and sparse characteristics of point cloud data align naturally with the permutation-invariant property of self-attention, which has motivated many recent transformer-based approaches.
Although these models exhibit strong global reasoning capability, they typically restrict attention operations to local windows for computational efficiency and extend receptive fields only indirectly through mechanisms such as window shifting or sparse sampling.
As a result, the receptive  field remains fixed, which limits flexibility in scenes where geometric structures vary significantly in scale, density, and arrangement.  

To address this limitation, this work introduces Dawin3D, a Transformer-based architecture designed to perform adaptive multiscale representation aggregation for 3D semantic segmentation.
The network is built around the Dynamic Window Multi-Head Self-Attention module, which applies self-attention at multiple spatial ranges and integrates the resulting representations through a learnable channel-wise weighting mechanism.
This adaptive fusion process compresses local features, applies nonlinear transformations, and identifies important channels that should receive greater influence, thereby improving feature expressiveness.  

The proposed pipeline partitions the 3D scene into a sparse voxel grid and associates each voxel with a representative feature, enabling efficient feature management under unstructured point distributions.
The effectiveness of the proposed adaptive feature aggregation strategy is validated through experiments on the ScanNet and S3DIS benchmarks.
The results show that adaptively weighting multiscale attention features provides a principled and effective approach for improving 3D scene understanding in environments characterized by structural variability and uneven spatial distributions.


## Overall Pipeline

![Overall arch](./asset/Overall.png)

The model takes either point clouds or meshes as input and utilizes the spatial coordinates and point attributes of scene points to extract discriminative features for segmentation.  

First, each scene is voxelized and feature representations are constructed through the Initial Feature Embedding module, which primarily employs sparse convolutional operations to efficiently capture spatial structures within the voxel grid.
The initialized voxel features are then progressively refined into high-level representations by a sequence of Dawin3D blocks, followed by downsampling at all stages except the final stage.
Finally, a UNet-style upsampling module and a classifier predict the semantic label for every point in the scene.


## DW-MSA3D

![Dawin3D block](./asset/Dawin3D_block.png)

The core of the Dawin3D block is the Dynamic Window Multi-head Self-Attention (DW-MSA) module.  

This module begins by reducing the dimensionality of the input voxel features through a fully connected layer, after which multi-head self-attention is performed under multiple window scales.
The attention outputs from different window sizes are then projected and combined by computing channel-wise weights through a squeeze-and-excitation strategy.
This mechanism enables the model to effectively integrate information across spatial regions of diverse scales.  


## Results

### Semantic Segmention on ScanNet (v2) and S3DIS
| Method                 | ScanNet Val mIoU | S3DIS Area 5 | S3DIS 6-fold |
|------------------------|------------------|---------------|---------------|
| KPConv [35]            | 68.4             | 67.1          | 70.6          |
| RepSurf [51]           | 70.0             | 68.9          | 74.3          |
| DeepViewAgg [52]       | 71.0             | 67.2          | 74.7          |
| MinkowskiNet [6]       | 72.1             | 65.4          | -             |
| Point Transformer [32] | -                | 70.4          | 73.5          |
| Stratified Transformer [33] | 74.3      | 72.0          | -             |
| Swin3D-S [34]          | 76.4             | **72.5**      | **76.9**      |
| Swin3D-L [34]          | 74.2             | -             | -             |
| **Dawin3D-S (Ours)**   | **76.4**         | 70.2          | 75.2          |
| **Dawin3D-L (Ours)**   | **76.7**         | 70.3          | 75.4          |
<br>

### Visual comparison
**Results on Scannet**
![Vis_ScanNet](./asset/ScanNet_vis_mod.jpg)
<br>

**Results on S3DIS**
![S3DIS](./asset/S3DIS_vis_mod.jpg)


## Environment Setup
**Clone the Dawin3D repository**

```
git clone https://github.com/hyebinny/Dawin3D.git
cd Dawin3d
```

**Install Dawin3D**

Install the required packages using the `requirements.txt` file:
```
pip install -r requirements.txt

cd Dawin3D_L/Dawin3D_L
python setup.py install
```

Alternatively, you may use Docker (recommended):
```
docker pull yukichiii/torch112_cu113:swin3d
```


## Data Preparation
**ScanNet (v2) Segmentation**  
Download the dataset from: https://github.com/ScanNet/ScanNet  
Refer to: https://github.com/dvlab-research/PointGroup for the ScanNetv2 preprocessing pipeline

**S3DIS Segmentation**  
Download the dataset from: https://sdss.redivis.com/datasets/9q3m-9w5pa1a2h  
Refer to: https://github.com/yanx27/Pointnet_Pointnet2_pytorch for the S3DIS preprocessing pipeline


## Training and Inference
**Train model**
```
cd Dawin3D_L/SemanticSeg
python train.py --config config/scannetv2/Dawin3D_RGBN_L.yaml
```

**Test model**
```
cd Dawin3D_L/SemanticSeg
python test.py --config config/scannetv2/Dawin3D_RGBN_L.yaml --vote_num 12 args.weight [ckpt_path]
```
For S3DIS testing, you can modify the `test_area` field in the configuration file to perform inference on each individual area.  

The trained models can be downloaded from the following link:  
https://drive.google.com/drive/folders/1C58dVrXNqGyQI03a5wQWESOkJHggnzCf?usp=drive_link


## Citation
```
bib
```

## Acknowledgment

This project is based on Swin3D (https://github.com/microsoft/Swin3D.git).
