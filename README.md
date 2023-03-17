# uestc-mmea-cl
for the dataset uestc-mmea-cl
# uestc-mmea-cl -- the new multi-modal activity dataset for continual egocentric activity recognition
<div align="center">
  <img src="resources/.png" width="700"/>
</div>

## Introduction  
_UESTC-MMEA-CL_ is a new multi-modal activity dataset for continual egocentric activity recognition, which is proposed to promote future studies on continual learning for first-person activity recognition in wearable applications. Our dataset provides not only vision data with auxiliary inertial sensor data but also comprehensive and complex daily activity categories for the purpose of continual learning research. UESTC-MMEA-CL comprises 30.4 hours of fully synchronized first-person video clips, acceleration stream and gyroscope data in total. There are 32 activity classes in the dataset and each class contains approximately 200 samples. We divide the samples of each class into the training set, validation set and test set according to the ratio of 7:2:1.

The work in the dataset publication paper is mainly based on [_TBN_](https://github.com/ekazakos/temporal-binding-network) and _PyCIL_ toolbox.

## Installation
* Linux
* pytorch
* Python 3.6+
* PyTorch 1.7
* CUDA 11.0 (If you build PyTorch from source, CUDA 9.0 is also compatible)
* GCC 5+


1. Please see [get_started.md](https://github.com/QiuHeqian/mmdetection-ref/blob/master/docs/get_started.md) for installation and the basic usage of UESTC-MMEA-CL.

2. Clone the repository and then install it: 

3. 
4. If you want to download the dataset [_UESTC-MMEA-CL_](https://ivipclab.github.io/publication_uestc-mmea-cl/mmea-cl/), Please click this hyperlin. Please ensure that it is used for educational or non-commercial purposes！

    ```
    UESTC-MMEA-CL/
          ├── train.txt
          ├── val.txt
          ├── test.txt
          ├── video/
          │  ├── 1_upstairs/
          │  ├──  ...
          │  └── 32_watch_TV/
          └── sensor/
              ├── 1_upstairs/
              ├──  ...
              └── 32_watch_TV/
    
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
