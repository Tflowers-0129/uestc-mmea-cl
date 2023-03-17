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
   
5. Pretrain Models: 
   You can modify load_from in corresponding config file to change the pretrained models.
    ```
    # assume that you want to use the config file 'configs/referring_grounding/refcoco/fcos_r101_concat_refcoco.py', you can change:
    load_from='pretrained_models/coco_train_minus_refer/fcos_r101.pth' #pretrained_models
    ```
## Train  

Assume that you have activated your virtual environment the code run needed, and with the dataset UESTC-MMEA-CL in path that  'data/....'. In the train process, we use 2GPUs.

运行之前，请在train.sh文件中修改好想进行训练的具体参数，例如
```
python main.py mydataset RGB Flow STFT STFT_2 --config ./exps/myewc.json --train_list mydataset_train.txt --val_list mydataset_test.txt --mpu_path '/home/amax/Downloads/whx/temporal-binding-network/dataset/gyro/' --arch BNInception --num_segments 8 --dropout 0.5 --epochs 20 -b 8 --lr 0.001 --lr_steps 10 20 --gd 20 --partialbn -j 8

```
修改参数之后，只需要运行以下代码
```
sh train.sh
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
cite...
```
