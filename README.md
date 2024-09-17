# Mask R-CNN for SOS

This is the detectron2 version for our [Segment Object System (SOS)](https://github.com/chwilms/SOS). Note that we only utilize detectron2's Mask R-CNN in SOS.

## Installation

Please follow the general installation instructions of detectron2 as described in [the original README.md](https://github.com/chwilms/SOS_detectron2/blob/main/README_detectron2.md).

## Usage

This version of detectron2 contains the new configuration files ([base](https://github.com/chwilms/SOS_detectron2/blob/main/configs/Base-RCNN-FPN_OWIS.yaml) and [ResNet-50 version](https://github.com/chwilms/SOS_detectron2/blob/main/configs/COCO-OpenWorldInstanceSegmentation/mask_rcnn_R_50_FPN_1x.yaml)) for training Mask R-CNN in the class-agnostic open-world instance segmentation setting described in the paper. We provide the base configurations with the full schedule assuming eight GPUs. Change the parameters accordingly for fewer GPUs. Note that in our study we only use 1/4 of the full schedule's iterations and scale the steps accordingly.

As the default dataset for training, we use the COCO training dataset with annotations of the VOC classes merged with pseudo annotations of the best object prior from our paper (*DINO* -> ```instances_train2017_voc_SOS_DINO.json``` -> ```coco_2017_train_voc_SOS_DINO```) as described in the paper (cross-category setup). You can find these annotations and the annotations based on the other object priors evaluated in our paper's study [in our main git](). By default, the test is carried out on COCO's validation set. 

To train Mask R-CNN as part of SOS, we provide an adapted training script [tools/train_net.py](https://github.com/chwilms/SOS_detectron2/blob/main/tools/train_net.py) to first automatically register all new annotation files generated as part of SOS for COCO's training dataset. We assume that the directory for the COCO dataset is provided as environmental variable ```DETECTRON2_DATASETS``` with subfolder ```coco/train2017``` for the training images and subfolder ```coco/annotations``` for the annotation files. All annotation files following our naming convention 

```
instances_train2017_voc_SOS_*.json
```

will be added automatically and are available in Mask R-CNN under the name 

```
coco_2017_train_voc_SOS_*
```

with ```*``` being an object prior name like *DINO*, as mentioned above. Set the parameter for the training dataset in the [configuration file](https://github.com/chwilms/SOS_detectron2/blob/main/configs/Base-RCNN-FPN_OWIS.yaml) or through the command line accordingly. Finally, Mask R-CNN will be trained and tested:

```
./tools/train_net.py --config-file ./configs/COCO-OpenWorldInstanceSegmentation/mask_rcnn_R_50_FPN_1x.yaml --num-gpus 8

```

If only inference is necessary, provide the path to the trained Mask R-CNN model ([see our main git for pre-trained models]()):

```
./tools/train_net.py --config-file ./configs/COCO-OpenWorldInstanceSegmentation/mask_rcnn_R_50_FPN_1x.yaml --num-gpus 8 --eval-only MODEL.WEIGHTS ./weights/SOS_DINO_coco_voc.pth
```

For evaluation on the cross-category setup (see paper), we use the code provided by [Saito et al.](https://ksaito-ut.github.io/openworld_ldet/).
