 # SmoothNet: A Plug-and-Play Network for Refining Human Poses in Videos

This repo is the official implementation of "**SmoothNet: A Plug-and-Play Network for Refining Human Poses in Videos**". 
[[Paper]](https://arxiv.org/abs/2112.13715)  [[Project]](https://ailingzeng.site/smoothnet)

## Update
- [x] Support SmoothNet in [MMPose](https://github.com/open-mmlab/mmpose) [Release v0.25.0](https://github.com/open-mmlab/mmpose/releases/tag/v0.25.0) as a smoothing strategy!

- [x] Clean version is released! 
It currently includes **code, data, log and models** for the following tasks: 
-  2D human pose estimation
- 3D human pose estimation
- Body recovery via a SMPL model

## TODO
- [ ] Support SmoothNet in [MMHuman3D](https://github.com/open-mmlab/mmhuman3d)


## Description

When analyzing human motion videos, the output jitters from existing pose estimators are highly-unbalanced. Most frames only suffer from slight jitters, while significant jitters occur in those frames with occlusion or poor image quality. Such complex poses often persist in videos, leading to consecutive frames with poor estimation results and large jitters. Existing pose smoothing solutions based on temporal convolutional networks, recurrent neural networks, or low-pass filters cannot deal with such a long-term jitter problem without considering the significant and persistent errors within the jittering video segment. Motivated by the above observation, we propose a novel plug-and-play refinement network, namely SMOOTHNET, which can be attached to any existing pose estimators to improve its temporal smoothness and enhance its per-frame precision simultaneously. Especially, SMOOTHNET is a simple yet effective data-driven fully-connected network with large receptive fields, effectively mitigating the impact of long-term jitters with unreliable estimation results. We conduct extensive experiments on twelve backbone networks with seven datasets across 2D and 3D pose estimation, body recovery, and downstream tasks. Our results demonstrate that the proposed SMOOTHNET consistently outperforms existing solutions, especially on those clips with high errors and long-term jitters.

### Major Features

- Model training and evaluation for **2D pose, 3D pose, and SMPL body representation**
- Supporting **6 popular datasets** ([AIST++](https://google.github.io/aistplusplus_dataset/factsfigures.html), [Human3.6M](http://vision.imar.ro/human3.6m/description.php), [Sub-JHMDB](http://jhmdb.is.tue.mpg.de/), [MPI-INF-3DHP](https://vcai.mpi-inf.mpg.de/3dhp-dataset/), [MuPoTS-3D](https://vcai.mpi-inf.mpg.de/projects/SingleShotMultiPerson/), [3DPW](https://virtualhumans.mpi-inf.mpg.de/3DPW/)) and providing cleaned estimation results of **13 popular pose estimation backbones**([SPIN](https://github.com/nkolot/SPIN), [TCMR](https://github.com/hongsukchoi/TCMR_RELEASE), [VIBE](https://github.com/mkocabas/VIBE), [CPN](https://github.com/chenyilun95/tf-cpn), [FCN](https://github.com/una-dinosauria/3d-pose-baseline), [Hourglass](http://www-personal.umich.edu/~alnewell/pose), [HRNet](https://github.com/leoxiaobin/deep-high-resolution-net.pytorch), [RLE](https://github.com/Jeff-sjtu/res-loglikelihood-regression), [VideoPose3D](https://github.com/facebookresearch/VideoPose3D), [TposeNet](https://github.com/vegesm/pose_refinement), [EFT](https://github.com/facebookresearch/eft), [PARE](https://pare.is.tue.mpg.de/), [SimplePose](https://github.com/microsoft/human-pose-estimation.pytorch))


## Results





## Getting Started

### Environment Requirement

SmoothNet has been implemented and tested on Pytorch 1.10.1 with python >= 3.6. It supports both GPU and CPU inference. 

Clone the repo:
```bash
git clone ??
```

We recommend you prepare the environment using `conda`:
```bash
# conda
source scripts/install_conda.sh
```

### Prepare Data

All the data used in our experiment can be downloaded here. 

[Google Drive](https://drive.google.com/drive/folders/1e5wEPWFNldihU5mBUpTOuQaGjgIxujrt?usp=sharing)

[Baidu Netdisk](https://pan.baidu.com/s/1ZBgQDJElkObHBhLsWtmkQw?pwd=cqcw)

The sructure of the repository should look like this:

```
|-- configs
    |-- aist_vibe_3D.yaml
    |-- ...
|-- data
    |-- checkpoints         # pretrained checkpoints
    |-- poses               # cleaned detected poses and groundtruth poses
    |-- smpl                # SMPL parameters
|-- lib
    |-- core
        |-- ...
    |-- dataset
        |-- ...
    |-- models
        |-- ...
    |-- utils
        |-- ...
|-- results                 # folders including log files, checkpoints, running config and tensorboard logs
|-- scripts
    |-- install_conda.sh
|-- eval_smoothnet.py       # SmoothNet evaluation
|-- train_smoothnet.py      # SmoothNet training
|-- README.md
|-- LICENSE
|-- requirements.txt
```


### Training

Run the commands below to start training:

```shell script
python train_smoothnet.py --cfg [config file] --dataset_name [dataset name] --estimator [backbone estimator you use] --body_representation [smpl/3D/2D] --slide_window_size [slide window size]
```

For example, you can train on 3D representation of Human3.6M using backbone estimator FCN with silde window size 8 by:

```shell script
python train.py --cfg configs/h36m_fcn_3D.yaml --dataset_name h36m --estimator fcn --body_representation 3D --slide_window_size 8
```

You can easily train on multiple datasets using "," split multiple datasets / estimator / body representation. For example, you can train on `AIST++` - `VIBE` - `3D` and `3DPW` - `SPIN` - `3D` with silde window size 8 by:

```shell script
python train.py --cfg configs/h36m_fcn_3D.yaml --dataset_name aist,pw3d --estimator vibe,spin --body_representation 3D,3D  --slide_window_size 8
```

Note that the training and testing datasets should be downloaded and prepared before training.

### Evaluation

Run the commands below to start evaluation:

```shell script
python eval_smoothnet.py --cfg [config file] --checkpoint [pretrained checkpoint] --dataset_name [dataset name] --estimator [backbone estimator you use] --body_representation [smpl/3D/2D] --slide_window_size [slide window size]
```

For example, you can evaluate `MPI-INF-3DHP` - `TCMR` - `3D` and `MPI-INF-3DHP` - `VIBE` - `3D` using SmoothNet trained on `3DPW` - `SPIN` - `3D` with silde window size 8 by:

```shell script
python train.py --cfg configs/pw3d_spin_3D.yaml --checkpoint data/checkpoints/pw3d_spin_3D/checkpoints_8.pth.tar --dataset_name mpiinf3dhp,mpiinf3dhp --estimator tcmr,vibe --body_representation 3D,3D --slide_window_size 8
```

Note that the pretrained checkpoints and testing datasets should be downloaded and prepared before evaluation.


## Citing SmoothNet

If you find this repository useful for your work, please consider citing it as follows:

```bibtex
@article{zeng2021smoothnet,
      title={SmoothNet: A Plug-and-Play Network for Refining Human Poses in Videos},
      author={Zeng, Ailing and Yang, Lei and Ju, Xuan and Li, Jiefeng and Wang, Jianyi and Xu, Qiang},
      journal={arXiv preprint arXiv:2112.13715},
      year={2021}}   
```

Please remember to cite all the datasets and backbone estimators if you use them in your experiments.


## License
This code is available for **non-commercial scientific research purposes** as defined in the [LICENSE file](./LICENSE). By downloading and using this code you agree to the terms in the [LICENSE](./LICENSE). Third-party datasets and software are subject to their respective licenses.
