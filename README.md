# EfficientDet (PyTorch)

This is a work in progress PyTorch implementation of EfficientDet. 

It is based on the
* official Tensorflow implementation by [Mingxing Tan and the Google Brain team](https://github.com/google/automl)
* and paper by Mingxing Tan, Ruoming Pang, Quoc V.Le [EfficientDet: Scalable and Efficient Object Detection](https://arxiv.org/abs/1911.09070) 

I am aware there are other PyTorch implementations. Their approach didn't fit well with my aim to replicate the Tensorflow models closely enough to allow weight ports while still maintaining a PyTorch feel and a high degree of flexibility for future additions. So, this is built from scratch and leverages my previous EfficientNet work.

## Updates / Tasks

### 2020-04-09
Initial code with working validation posted. Yes, it's a little slow, but I think faster than the official impl on a GPU if you leave AMP enabled. Post processing needs some love. 

### Core Tasks
- [x] Feature extraction from my EfficientNet implementations (https://github.com/rwightman/gen-efficientnet-pytorch or https://github.com/rwightman/pytorch-image-models)
- [x] Low level blocks / helpers (SeparableConv, create_pool2d (same padding), etc)
- [x] PyTorch implementation of BiFPN, BoxNet, ClassNet modules and related submodules
- [x] Port Tensorflow checkpoints to PyTorch -- initial D1 checkpoint converted, state_dict loaded, on to validation....
- [x] Basic MS COCO validation script
  - [x] Temporary (hacky) COCO dataset and transform 
  - [x] Port reference TF anchor and object detection code
  - [x] Verify model output sanity (definitely signs of life with some valid predictions, has issues, likely alignment/padding/missed activation)
  - [X] Integrate MSCOCO eval metric calcs
- [x] Some cleanup, testing
- [ ] Add torch hub support and pretrained URL based weight download
- [ ] Change module dependencies from 'timm' to minimal 'geffnet' for backbone, bring some of the layers here
- [ ] Remove redundant bias layers that exist in the official impl and weights
- [ ] Add visualization support
- [ ] Performance improvements, numpy TF detection code -> optimized PyTorch

### Possible Future Tasks
- [ ] Training (object detection) reimplementation w/ Rand/AutoAugment, etc
- [ ] Training (semantic segmentation) experiments
- [ ] Integration with Detectron2 / MMDetection codebases
- [ ] Addition and cleanup of EfficientNet based U-Net and DeepLab segmentation models that I've used in past projects
- [ ] Addition and cleanup of OpenImages dataset/training support from a past project
- [ ] Exploration of instance segmentation possibilities...

Before the COVID-19 madness changed priorities I was looking into signing up for GitHub Sponsors. I've decided for now to focus on building. However, if you are an organization interested in sponsoring and any of this work, or prioritization of the possible future directions interests you, feel free to contact me (issue, LinkedIn, Twitter, hello at rwightman dot com)

## Models

| Variant | Download | mAP (val2017) | mAP (test-dev2017) | mAP (Tensorflow official test-dev2017) |
| --- | --- | :---: | :---: | :---: |
| D0 | [tf_efficientdet_d0.pth](https://github.com/rwightman/efficientdet-pytorch/releases/download/v0.1/efficientdet_d0-d92fd44f.pth) | 32.8 | TBD | 33.8 |
| D1 | [tf_efficientdet_d1.pth](https://github.com/rwightman/efficientdet-pytorch/releases/download/v0.1/efficientdet_d1-4c7ebaf2.pth) | 38.5 | TBD | 39.6 |
| D2 | [tf_efficientdet_d2.pth](https://github.com/rwightman/efficientdet-pytorch/releases/download/v0.1/efficientdet_d2-cb4ce77d.pth) | 42.0 | 42.5 | 43 |
| D3 | [tf_efficientdet_d3.pth](https://github.com/rwightman/efficientdet-pytorch/releases/download/v0.1/efficientdet_d3-b0ea2cbc.pth) | 45.3 | TBD | 45.8 | 
| D4 | [tf_efficientdet_d4.pth](https://github.com/rwightman/efficientdet-pytorch/releases/download/v0.1/efficientdet_d4-5b370b7a.pth) | 48.3 | TBD | 49.4 |
| D5 | [tf_efficientdet_d5.pth](https://github.com/rwightman/efficientdet-pytorch/releases/download/v0.1/efficientdet_d5-ef44aea8.pth) | 49.6 | TBD | 50.7 |
| D6 | [tf_efficientdet_d6.pth](https://github.com/rwightman/efficientdet-pytorch/releases/download/v0.1/efficientdet_d6-51cb0132.pth) | 50.6 | TBD | 51.7 |
| D7 | [tf_efficientdet_d7.pth](https://github.com/rwightman/efficientdet-pytorch/releases/download/v0.1/efficientdet_d7-f05bf714.pth) | 50.9 | TBD | 52.2 |


## Usage

Tested in a Python 3.7 or 3.8 conda environment in Linux with:
* PyTorch 1.4
* PyTorch Image Models (timm) 0.1.20, `pip install timm` or local install from (https://github.com/rwightman/pytorch-image-models) 
* Apex AMP master (as of 2020-04)

*NOTE* - There is a conflict/bug with Numpy 1.18+ and pycocotools, force install numpy <= 1.17.5 or the coco eval will fail,
the validation script will still save the output JSON and that can be run through eval again later. 


Run validation (val2017 by default) with D2 model: `python validation.py /localtion/of/mscoco/ --model tf_efficientdet_d2 --checkpoint tf_efficientdet_d2.pth`

Run test-dev2017: `python validation.py /localtion/of/mscoco/ --model tf_efficientdet_d2 --checkpoint tf_efficientdet_d2.pth --anno test-dev2017`

## Results

### TEST-DEV2017

NOTE: I've only tried submitting D2 to dev server for sanity check so far

#### EfficientDet-D2

```
 Average Precision  (AP) @[ IoU=0.50:0.95 | area=   all | maxDets=100 ] = 0.425
 Average Precision  (AP) @[ IoU=0.50      | area=   all | maxDets=100 ] = 0.618
 Average Precision  (AP) @[ IoU=0.75      | area=   all | maxDets=100 ] = 0.457
 Average Precision  (AP) @[ IoU=0.50:0.95 | area= small | maxDets=100 ] = 0.222
 Average Precision  (AP) @[ IoU=0.50:0.95 | area=medium | maxDets=100 ] = 0.467
 Average Precision  (AP) @[ IoU=0.50:0.95 | area= large | maxDets=100 ] = 0.578
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets=  1 ] = 0.341
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets= 10 ] = 0.536
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets=100 ] = 0.569
 Average Recall     (AR) @[ IoU=0.50:0.95 | area= small | maxDets=100 ] = 0.334
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=medium | maxDets=100 ] = 0.627
 Average Recall     (AR) @[ IoU=0.50:0.95 | area= large | maxDets=100 ] = 0.749
```

### VAL2017

#### EfficientDet-D0
```
 Average Precision  (AP) @[ IoU=0.50:0.95 | area=   all | maxDets=100 ] = 0.328
 Average Precision  (AP) @[ IoU=0.50      | area=   all | maxDets=100 ] = 0.509
 Average Precision  (AP) @[ IoU=0.75      | area=   all | maxDets=100 ] = 0.346
 Average Precision  (AP) @[ IoU=0.50:0.95 | area= small | maxDets=100 ] = 0.118
 Average Precision  (AP) @[ IoU=0.50:0.95 | area=medium | maxDets=100 ] = 0.380
 Average Precision  (AP) @[ IoU=0.50:0.95 | area= large | maxDets=100 ] = 0.520
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets=  1 ] = 0.283
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets= 10 ] = 0.432
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets=100 ] = 0.457
 Average Recall     (AR) @[ IoU=0.50:0.95 | area= small | maxDets=100 ] = 0.185
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=medium | maxDets=100 ] = 0.539
 Average Recall     (AR) @[ IoU=0.50:0.95 | area= large | maxDets=100 ] = 0.679
```

#### EfficientDet-D1
```
 Average Precision  (AP) @[ IoU=0.50:0.95 | area=   all | maxDets=100 ] = 0.385
 Average Precision  (AP) @[ IoU=0.50      | area=   all | maxDets=100 ] = 0.575
 Average Precision  (AP) @[ IoU=0.75      | area=   all | maxDets=100 ] = 0.410
 Average Precision  (AP) @[ IoU=0.50:0.95 | area= small | maxDets=100 ] = 0.179
 Average Precision  (AP) @[ IoU=0.50:0.95 | area=medium | maxDets=100 ] = 0.441
 Average Precision  (AP) @[ IoU=0.50:0.95 | area= large | maxDets=100 ] = 0.561
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets=  1 ] = 0.318
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets= 10 ] = 0.491
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets=100 ] = 0.519
 Average Recall     (AR) @[ IoU=0.50:0.95 | area= small | maxDets=100 ] = 0.278
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=medium | maxDets=100 ] = 0.589
 Average Recall     (AR) @[ IoU=0.50:0.95 | area= large | maxDets=100 ] = 0.721
```



#### EfficientDet-D2
```
 Average Precision  (AP) @[ IoU=0.50:0.95 | area=   all | maxDets=100 ] = 0.420
 Average Precision  (AP) @[ IoU=0.50      | area=   all | maxDets=100 ] = 0.612
 Average Precision  (AP) @[ IoU=0.75      | area=   all | maxDets=100 ] = 0.448
 Average Precision  (AP) @[ IoU=0.50:0.95 | area= small | maxDets=100 ] = 0.232
 Average Precision  (AP) @[ IoU=0.50:0.95 | area=medium | maxDets=100 ] = 0.476
 Average Precision  (AP) @[ IoU=0.50:0.95 | area= large | maxDets=100 ] = 0.582
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets=  1 ] = 0.338
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets= 10 ] = 0.531
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets=100 ] = 0.562
 Average Recall     (AR) @[ IoU=0.50:0.95 | area= small | maxDets=100 ] = 0.340
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=medium | maxDets=100 ] = 0.627
 Average Recall     (AR) @[ IoU=0.50:0.95 | area= large | maxDets=100 ] = 0.741
```

#### EfficientDet-D3
```
 Average Precision  (AP) @[ IoU=0.50:0.95 | area=   all | maxDets=100 ] = 0.453
 Average Precision  (AP) @[ IoU=0.50      | area=   all | maxDets=100 ] = 0.646
 Average Precision  (AP) @[ IoU=0.75      | area=   all | maxDets=100 ] = 0.485
 Average Precision  (AP) @[ IoU=0.50:0.95 | area= small | maxDets=100 ] = 0.272
 Average Precision  (AP) @[ IoU=0.50:0.95 | area=medium | maxDets=100 ] = 0.498
 Average Precision  (AP) @[ IoU=0.50:0.95 | area= large | maxDets=100 ] = 0.614
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets=  1 ] = 0.355
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets= 10 ] = 0.563
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets=100 ] = 0.597
 Average Recall     (AR) @[ IoU=0.50:0.95 | area= small | maxDets=100 ] = 0.399
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=medium | maxDets=100 ] = 0.647
 Average Recall     (AR) @[ IoU=0.50:0.95 | area= large | maxDets=100 ] = 0.762
```

#### EfficientDet-D4
 ```
 Average Precision  (AP) @[ IoU=0.50:0.95 | area=   all | maxDets=100 ] = 0.483
 Average Precision  (AP) @[ IoU=0.50      | area=   all | maxDets=100 ] = 0.680
 Average Precision  (AP) @[ IoU=0.75      | area=   all | maxDets=100 ] = 0.521
 Average Precision  (AP) @[ IoU=0.50:0.95 | area= small | maxDets=100 ] = 0.325
 Average Precision  (AP) @[ IoU=0.50:0.95 | area=medium | maxDets=100 ] = 0.535
 Average Precision  (AP) @[ IoU=0.50:0.95 | area= large | maxDets=100 ] = 0.634
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets=  1 ] = 0.371
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets= 10 ] = 0.591
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets=100 ] = 0.626
 Average Recall     (AR) @[ IoU=0.50:0.95 | area= small | maxDets=100 ] = 0.453
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=medium | maxDets=100 ] = 0.676
 Average Recall     (AR) @[ IoU=0.50:0.95 | area= large | maxDets=100 ] = 0.772
```

#### EfficientDet-D5
```
 Average Precision  (AP) @[ IoU=0.50:0.95 | area=   all | maxDets=100 ] = 0.496
 Average Precision  (AP) @[ IoU=0.50      | area=   all | maxDets=100 ] = 0.692
 Average Precision  (AP) @[ IoU=0.75      | area=   all | maxDets=100 ] = 0.533
 Average Precision  (AP) @[ IoU=0.50:0.95 | area= small | maxDets=100 ] = 0.327
 Average Precision  (AP) @[ IoU=0.50:0.95 | area=medium | maxDets=100 ] = 0.543
 Average Precision  (AP) @[ IoU=0.50:0.95 | area= large | maxDets=100 ] = 0.642
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets=  1 ] = 0.377
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets= 10 ] = 0.608
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets=100 ] = 0.644
 Average Recall     (AR) @[ IoU=0.50:0.95 | area= small | maxDets=100 ] = 0.469
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=medium | maxDets=100 ] = 0.688
 Average Recall     (AR) @[ IoU=0.50:0.95 | area= large | maxDets=100 ] = 0.785
```

#### EfficientDet-D6
```
 Average Precision  (AP) @[ IoU=0.50:0.95 | area=   all | maxDets=100 ] = 0.506
 Average Precision  (AP) @[ IoU=0.50      | area=   all | maxDets=100 ] = 0.701
 Average Precision  (AP) @[ IoU=0.75      | area=   all | maxDets=100 ] = 0.545
 Average Precision  (AP) @[ IoU=0.50:0.95 | area= small | maxDets=100 ] = 0.336
 Average Precision  (AP) @[ IoU=0.50:0.95 | area=medium | maxDets=100 ] = 0.552
 Average Precision  (AP) @[ IoU=0.50:0.95 | area= large | maxDets=100 ] = 0.650
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets=  1 ] = 0.382
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets= 10 ] = 0.616
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets=100 ] = 0.653
 Average Recall     (AR) @[ IoU=0.50:0.95 | area= small | maxDets=100 ] = 0.477
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=medium | maxDets=100 ] = 0.697
 Average Recall     (AR) @[ IoU=0.50:0.95 | area= large | maxDets=100 ] = 0.791
```

#### EfficientDet-D7
 ```
 Average Precision  (AP) @[ IoU=0.50:0.95 | area=   all | maxDets=100 ] = 0.509
 Average Precision  (AP) @[ IoU=0.50      | area=   all | maxDets=100 ] = 0.705
 Average Precision  (AP) @[ IoU=0.75      | area=   all | maxDets=100 ] = 0.548
 Average Precision  (AP) @[ IoU=0.50:0.95 | area= small | maxDets=100 ] = 0.353
 Average Precision  (AP) @[ IoU=0.50:0.95 | area=medium | maxDets=100 ] = 0.559
 Average Precision  (AP) @[ IoU=0.50:0.95 | area= large | maxDets=100 ] = 0.648
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets=  1 ] = 0.381
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets= 10 ] = 0.617
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets=100 ] = 0.654
 Average Recall     (AR) @[ IoU=0.50:0.95 | area= small | maxDets=100 ] = 0.489
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=medium | maxDets=100 ] = 0.696
 Average Recall     (AR) @[ IoU=0.50:0.95 | area= large | maxDets=100 ] = 0.791
 ```