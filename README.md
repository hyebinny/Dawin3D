
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


## Abstract

Designing computationally efficient network architectures persists as an ongoing necessity in computer vision. In this paper, we transplant Mamba, a state-space language model, into VMamba, a vision backbone that works in linear time complexity. At the core of VMamba lies a stack of Visual State-Space (VSS) blocks with the 2D Selective Scan (SS2D) module. By traversing along four scanning routes, SS2D helps bridge the gap between the ordered nature of 1D selective scan and the non-sequential structure of 2D vision data, which facilitates the gathering of contextual information from various sources and perspectives. Based on the VSS blocks, we develop a family of VMamba architectures and accelerate them through a succession of architectural and implementation enhancements. Extensive experiments showcase VMamba’s promising performance across diverse visual perception tasks, highlighting its advantages in input scaling efficiency compared to existing benchmark models.

## Overview
(./assets/Overall.png)



### Semantic Segmenttion on ScanNet (v2) and S3DIS
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


### Visual comparison
(./assets/Vis.png)



## Getting Started

### Installation

**Step 1: Clone the VMamba repository:**

To get started, first clone the VMamba repository and navigate to the project directory:

```bash
git clone https://github.com/MzeroMiko/VMamba.git
cd VMamba
```

**Step 2: Environment Setup:**

VMamba recommends setting up a conda environment and installing dependencies via pip. Use the following commands to set up your environment:
Also, We recommend using the pytorch>=2.0, cuda>=11.8. But lower version of pytorch and CUDA are also supported.

***Create and activate a new conda environment***

```bash
conda create -n vmamba
conda activate vmamba
```

***Install Dependencies***

```bash
pip install -r requirements.txt
cd kernels/selective_scan && pip install .
```
<!-- cd kernels/cross_scan && pip install . -->

***Check Selective Scan (optional)***

* If you want to check the modules compared with `mamba_ssm`, install [`mamba_ssm`](https://github.com/state-spaces/mamba) first!

* If you want to check if the implementation of `selective scan` of ours is the same with `mamba_ssm`, `selective_scan/test_selective_scan.py` is here for you. Change to `MODE = "mamba_ssm_sscore"` in `selective_scan/test_selective_scan.py`, and run `pytest selective_scan/test_selective_scan.py`.

* If you want to check if the implementation of `selective scan` of ours is the same with reference code (`selective_scan_ref`), change to `MODE = "sscore"` in `selective_scan/test_selective_scan.py`, and run `pytest selective_scan/test_selective_scan.py`.

* `MODE = "mamba_ssm"` stands for checking whether the results of `mamba_ssm` is close to `selective_scan_ref`, and `"sstest"` is preserved for development. 

* If you find `mamba_ssm` (`selective_scan_cuda`) or `selective_scan` ( `selctive_scan_cuda_core`) is not close enough to `selective_scan_ref`, and the test failed, do not worry. Check if `mamba_ssm` and `selective_scan` are close enough [instead](https://github.com/state-spaces/mamba/pull/161).

* ***If you are interested in selective scan, you can check [mamba](https://github.com/state-spaces/mamba), [mamba-mini](https://github.com/MzeroMiko/mamba-mini), [mamba.py](https://github.com/alxndrTL/mamba.py) [mamba-minimal](https://github.com/johnma2006/mamba-minimal) for more information.***

***Dependencies for `Detection` and `Segmentation` (optional)***

```bash
pip install mmengine==0.10.1 mmcv==2.1.0 opencv-python-headless ftfy regex
pip install mmdet==3.3.0 mmsegmentation==1.2.2 mmpretrain==1.2.0
```

### Model Training and Inference

**Classification**

To train VMamba models for classification on ImageNet, use the following commands for different configurations:

```bash
python -m torch.distributed.launch --nnodes=1 --node_rank=0 --nproc_per_node=8 --master_addr="127.0.0.1" --master_port=29501 main.py --cfg </path/to/config> --batch-size 128 --data-path </path/of/dataset> --output /tmp
```

If you only want to test the performance (together with params and flops):

```bash
python -m torch.distributed.launch --nnodes=1 --node_rank=0 --nproc_per_node=1 --master_addr="127.0.0.1" --master_port=29501 main.py --cfg </path/to/config> --batch-size 128 --data-path </path/of/dataset> --output /tmp --pretrained </path/of/checkpoint>
```

***please refer to [modelcard](./modelcard.sh) for more details.***

**Detection and Segmentation**

To evaluate with `mmdetection` or `mmsegmentation`:
```bash
bash ./tools/dist_test.sh </path/to/config> </path/to/checkpoint> 1
```
*use `--tta` to get the `mIoU(ms)` in segmentation*

To train with `mmdetection` or `mmsegmentation`:
```bash
bash ./tools/dist_train.sh </path/to/config> 8
```

For more information about detection and segmentation tasks, please refer to the manual of [`mmdetection`](https://mmdetection.readthedocs.io/en/latest/user_guides/train.html) and [`mmsegmentation`](https://mmsegmentation.readthedocs.io/en/latest/user_guides/4_train_test.html). Remember to use the appropriate backbone configurations in the `configs` directory.

### Analysis Tools


## Citation

```
bib
```

## Acknowledgment

This project is based on Swin3D (https://github.com/microsoft/Swin3D.git).
