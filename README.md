# Train a NN to classify images and optimize cpu inference with OpenVINO in 10mins

## Basic Requirements
- Latest stable Docker and unzip installed on your Linux System
- Network connectivity to hub.docker.com (instructions assume no proxy)
- 6th to 10th generation Intel Core or Intel Xeon processors or 3rd generation Intel® Xeon® Scalable processors

## Train and export a custom model in your browser
Check out the Medium Article for visual steps: URL

- Navigate to https://teachablemachine.withgoogle.com/train and start the "Image Project"
- Edit Class Names to labels (add as many), record using the webcam or file upload
- Hit **Train Model** (don't switch tabs while training)
- On the *Preview panel*, hit **Export Model**
- On the *Export your model to use it projects*, click the second tab **Tensorflow**
- With *Keras* already selected, click the **Download my model**

## Convert any TM2.0: Image Project Keras model to OpenVINO IR format
Replace the downloaded converted_keras.zip file in the current directory (alongside this readme file) and run below script in the repo root folder.
```
./util_conv_teachable_to_openvino.sh
```
Notes:
- Pulls necessary docker images (openvino/ubuntu18_dev:latest, tensorflow/tensorflow:1.15.0)
- Extracts the zip file (keras_model.h5, labels.txt)
- Removes training nodes and converts .h5 to frozen .pb (frozen_model.pb)
- Converts .pb to OpenVINO IR (frozen_model.xml, frozen_model.bin, frozen_model.mapping)

## Run OpenVINO Inference on a single image
Make sure to capture your test images pertaining to your custom trained model.
```
docker run --rm -it -v ${PWD}:/workdir openvino/ubuntu18_dev:latest /bin/bash
cd /workdir
python3 teachable_img_openvino_classify.py frozen_model.xml frozen_model.bin labels.txt test.jpg
```
Note: The python script is only **30 lines** of actual code to run cpu optimized inference of a teachable machine 2.0 trained image classification model.

## Run OpenVINO Inference on a live webcam
```
xhost +
docker run --rm -it --privileged -v ${PWD}:/workdir -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix -v /dev/video0:/dev/video0 openvino/ubuntu18_dev:latest /bin/bash
cd /workdir
python3 teachable_livecam_openvino_classify.py frozen_model.xml frozen_model.bin labels.txt
```

## Run the original teachable machine example for performance comparsion
```
docker run --rm -it -v ${PWD}:/workdir tensorflow/tensorflow:1.15.0 bash
cd /workdir
pip install Pillow
python teachable_img_keras_orig_classify.py keras_model.h5 test.jpg
```

## Known Limitations
- Only Teachable Machine 2.0: Image projects are covered for now.
- Depending upon popularity, Audio and Pose projects can be covered in the future.


