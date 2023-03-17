# uestc-mmea-cl
for the dataset uestc-mmea-cl
# MMDetection-Ref: An Open-Source Referring Grounding Toolbox
<div align="center">
  <img src="resources/mmdetection-ref_framework.png" width="700"/>
</div>

## Introduction  
_UESTC-MMEA-CL_ is a new multi-modal activity dataset for continual egocentric activity recognition, which is proposed to promote future studies on continual learning for first-person activity recognition in wearable applications. Our dataset provides not only vision data with auxiliary inertial sensor data but also comprehensive and complex daily activity categories for the purpose of continual learning research. UESTC-MMEA-CL comprises 30.4 hours of fully synchronized first-person video clips, acceleration stream and gyroscope data in total. There are 32 activity classes in the dataset and each class contains approximately 200 samples. We divide the samples of each class into the training set, validation set and test set according to the ratio of 7:2:1.


is an o  based on [TBN](https://github.com/ekazakos/temporal-binding-network), which ﬂexibly supports the integration of natural language and various visual detectors for end-to-end referring expression comprehension task.

## Installation
* Linux or macOS (Windows is in experimental support)
* MMDetection
* pytorch
* Python 3.6+
* PyTorch 1.3+
* CUDA 9.2+ (If you build PyTorch from source, CUDA 9.0 is also compatible)
* GCC 5+
* h5py
* spacy

1. Please see [get_started.md](https://github.com/QiuHeqian/mmdetection-ref/blob/master/docs/get_started.md) for installation and the basic usage of MMDetection-Ref.

2. Clone the repository and then install it: 
``` 
git clone https://github.com/QiuHeqian/mmdetection-ref.git
cd mmdetection-ref  
pip install -v -e .  # or "python setup.py develop"  
```
3. Download the [RefCoco annotations](https://drive.google.com/file/d/19pY_Ks48FNflXp2zt-9jQRLiYn5oFMj8/view?usp=sharing) and the images ([train2014 of MS-COCO dataset](http://images.cocodataset.org/zips/train2014.zip)), please place them in 'data/RefCoco'.
4. If you want to download [RefCrowd dataset](https://qiuheqian.github.io/datasets/refcrowd/) for grounding person in crowd, you requires to sign [RefCrowd Terms of Use](https://docs.google.com/forms/d/e/1FAIpQLSeWgI0mzT1OFrhEthLZLURvuC-he_Hy882nEnCzhiM6DAoFEg/viewform?usp=sf_link) as restrictions on access to dataset to privacy protection and use dataset for non-commercial research and/or educational purposes. After downloading RefCrowd dataset, please place them in 'data/RefCrowd'.
    ```
    mmdetection-ref
    ├── data
    │   ├── RefCoco
    │   │   ├── refcoco
    │   │   │   ├── train.json
    │   │   │   ├── val.json
    │   │   │   ├── testA.json
    │   │   │   ├── testB.json
    │   │   ├── refcoco+
    │   │   │   ├── train.json
    │   │   │   ├── val.json
    │   │   │   ├── testA.json
    │   │   │   ├── testB.json
    │   │   ├── refcocog
    │   │   │   ├── train.json
    │   │   │   ├── val.json
    │   │   │   ├── test.json
    │   │   ├── train2014
    │   ├── RefCrowd
    │   │   ├── annotations
    │   │   │   ├── train.json
    │   │   │   ├── val.json
    │   │   │   ├── test.json
    │   │   ├── images
    │   │   │   ├── train
    │   │   │   ├── val
    │   │   │   ├── test

    ```
5. Pretrain Models: download the [pretrained models](https://drive.google.com/drive/folders/1uAxYujoKWIDngG5VpNzpKlb5KJTuzBdw?usp=sharing) and place the file in 'pretrained_models/'. You can get two types of pretrained models: [coco_train](https://drive.google.com/file/d/1ie3Y4zznidCzbTYmtz4QUK0dY9cwHthx/view?usp=sharing) indicates models are trained using all COCO training data, [coco_train_minus_refer](https://drive.google.com/file/d/1FZEm9F0zSzjewGzr64sEFP6mPEgRxUCN/view?usp=sharing) indicates models are trained excluding COCO training data in RefCOCO, RefCOCO+, and RefCOCOg’s validation+testing.

   You can modify load_from in corresponding config file to change the pretrained models.
    ```
    # assume that you want to use the config file 'configs/referring_grounding/refcoco/fcos_r101_concat_refcoco.py', you can change:
    load_from='pretrained_models/coco_train_minus_refer/fcos_r101.pth' #pretrained_models
    ```
## Train  

Assume that you are under the root directory of this project, and you have activated your virtual environment if needed, and with refcoco dataset in 'data/RefCoco/refcoco'. Here, we use 8GPUs.
```
#refcoco using plain concatenatation to fuse visual features and language features 
#based on FCOS-R101 detectors.
./tools/dist_train.sh configs/referring_grounding/refcoco/fcos_r101_concat_refcoco.py 8 

```
```
#refcoco using dynamic filters to fuse visual features and language features 
#based on FCOS-R101 detectors.
./tools/dist_train.sh configs/referring_grounding/refcoco/fcos_r101_dynamic_refcoco.py 8 
```

## Inference
If you want to evaluate the split set in refcoco, you can modify the test in corresponding config file to change the evaluation dataset.
```
test=dict(
        type=dataset_type,
        ann_file=data_root+'refcoco/val.json', #val.json, testA.json, testB.json
        img_prefix=data_root + 'train2014/', #val imageset, testA imageset, testB imageset, 
        pipeline=test_pipeline))
```
Then, you can use the following script for inference using Top1Acc metric.
```
#refcoco using dynamic filters to fuse visual features and language features 
#based on FCOS-R101 detectors.
./tools/dist_test.sh configs/referring_grounding/refcoco/fcos_r101_concat_refcoco.py work_dirs/fcos_r101_concat_refcoco/epoch_12.pth 8 --eval Top1Acc
```
Training and inference for other datasets in different config files are similar to the above description.

## Performance and Trained Models
The performance and trained models will be released soon, please wait...
## Acknowledgement
Thanks MMDetection team for the wonderful open source project!

## Citition
If you find the mmdetection-ref toolbox useful in your research, please consider citing:  
```
@inproceedings{qiu2020language,
  title={Language-aware fine-grained object representation for referring expression comprehension},
  author={Qiu, Heqian and Li, Hongliang and Wu, Qingbo and Meng, Fanman and Shi, Hengcan and Zhao, Taijin and Ngan, King Ngi},
  booktitle={Proceedings of the 28th ACM International Conference on Multimedia},
  pages={4171--4180},
  year={2020}
}
@inproceedings{qiu2022refcrowd,
  title={RefCrowd: Grounding the Target in Crowd with Referring Expressions},
  author={Qiu, Heqian and Li, Hongliang and Zhao, Taijin and Wang, Lanxiao and Wu, Qingbo and Meng, Fanman},
  booktitle={Proceedings of the 30th ACM International Conference on Multimedia},
  pages={4435--4444},
  year={2022}
}
```
