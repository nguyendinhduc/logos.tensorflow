# logos.tensorflow

## The Problem
We would like to detect brand logos in an image.

## TODO

- [x] Project setup
- [x] Data Preprocessing
- [x] Train models
- [x] Save results
- [x] Write readme with results
- [ ] Add training plots to readme
- [ ] Tests
- [ ] CI/CD
- [ ] API
- [ ] Deploy
- [ ] Train with distractor logo images


## Setup Project
I recommended use docker and work inside container
```bash
docker build -f ./dockerfiles/gpu/Dockerfile -t logost.tensorflow .
nvidia-docker run -it -v $PWD:/code --net=host --ipc=host logost.tensorflow:latest /bin/bash
```

## Dataset
You can find origin dataset [here](http://image.ntua.gr/iva/datasets/flickr_logos/)
Also you can download my train/val/test split with tf.records [here](https://s3-us-west-2.amazonaws.com/logos.tensorflow/dataset.tar.gz)

Or you can create your own dataset
```bash
mkdir dataset
./scripts/load_data.sh
python ./logos/data_preprocessing.py
python ./logos/create_tf_records.py
```

## Results
|    | name                        |   speed (ms) |   train mAP |   val mAP | test images                                                                                                | pipeline                                                       | checkpoints                                                                                                |
|---:|:----------------------------|-------------:|------------:|----------:|:-----------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------|
|  0 | ssd_mobilenet_v1            |           30 |      0.9088 |    0.6811 | [load](https://s3-us-west-2.amazonaws.com/logos.tensorflow/ssd_mobilenet_v1_test_images.tar.gz)            | [pipeline](./model_configs/ssd_mobilenet_v1.config)            | [load](https://s3-us-west-2.amazonaws.com/logos.tensorflow/ssd_mobilenet_v1_checkpoint.tar.gz)             |
|  1 | ssd_mobilenet_v1_focal_loss |           30 |      0.9518 |    0.671  | [load](https://s3-us-west-2.amazonaws.com/logos.tensorflow/ssd_mobilenet_v1_focal_loss_test_images.tar.gz) | [pipeline](./model_configs/ssd_mobilenet_v1_focal_loss.config) | [load](https://s3-us-west-2.amazonaws.com/logos.tensorflow/ssd_mobilenet_v1_focal_loss_checkpoint.tar.gz) |
|  2 | faster_rcnn_resnet50        |           89 |      0.9893 |    0.7395 | [load](https://s3-us-west-2.amazonaws.com/logos.tensorflow/faster_rcnn_resnet50_test_images.tar.gz)        | [pipeline](./model_configs/ssd_mobilenet_v1.config)            | [load](https://s3-us-west-2.amazonaws.com/logos.tensorflow/faster_rcnn_resnet50_checkpoint.tar.gz)         |


## Commands for tensorflow object detection API
Parameters
```bash
export PATH_TO_YOUR_PIPELINE_CONFIG=...
export PATH_TO_TRAIN_DIR=...
export PATH_TO_EVAL_DIR=...
export TRAIN_PATH=...
export EXPORT_DIR=...
```
Train/Eval/Export model
```bash
python object_detection/train.py \
    --logtostderr \
    --pipeline_config_path=${PATH_TO_YOUR_PIPELINE_CONFIG} \
    --train_dir={PATH_TO_TRAIN_DIR}

python object_detection/eval.py \
    --logtostderr \
    --pipeline_config_path=${PATH_TO_YOUR_PIPELINE_CONFIG} \
    --checkpoint_dir=${PATH_TO_TRAIN_DIR} \
    --eval_dir=${PATH_TO_TRAIN_DIR}

python object_detection/export_inference_graph.py \
    --input_type image_tensor \
    --pipeline_config_path=${PATH_TO_YOUR_PIPELINE_CONFIG} \
    --trained_checkpoint_prefix=${TRAIN_PATH} \
    --output_directory=${EXPORT_DIR}

```

## References
- [Speed/accuracy trade-offs for modern convolutional object detectors](https://arxiv.org/abs/1611.10012)
- [Faster R-CNN: Towards Real-Time Object Detection with Region Proposal Networks](http://arxiv.org/abs/1506.01497)
- [Focal Loss for Dense Object Detection](https://arxiv.org/abs/1708.02002)
- [A systematic study of the class imbalance problem in convolutional neural networks](https://arxiv.org/abs/1710.05381)
- [SSD: Single Shot MultiBox Detector](https://arxiv.org/abs/1512.02325)
- [MobileNetV2: Inverted Residuals and Linear Bottlenecks](https://arxiv.org/abs/1801.04381)
- [Faster R-CNN Explained](https://medium.com/@smallfishbigsea/faster-r-cnn-explained-864d4fb7e3f8)
- [Understanding SSD MultiBox — Real-Time Object Detection In Deep Learning](https://towardsdatascience.com/understanding-ssd-multibox-real-time-object-detection-in-deep-learning-495ef744fab)
