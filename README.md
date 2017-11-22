# Object Detection CNN
Object detection using convolutional neural networks with already trained <a href="https://pjreddie.com/darknet/yolo/">YOLO (You Only Look Once)</a> model.

<br/>
<p align="center"><img src="https://user-images.githubusercontent.com/24521991/33061502-553d2b4e-ced7-11e7-9a11-3e60e576d180.png" width="900"></p>

## Notes

### Trained model
<ul>
<li>Download trained model (size >25 MB) from <a href="https://pjreddie.com/media/files/yolo-voc.weights">here</a>.</li>
<li>Change name to "yolo.h5"</li>
<li>Deploy in /model_data folder</li>
</ul>

### Input image
For each image:
<ul>
<li>Update <b>image_shape</b> in car_detection_yolo.py line 96:</li>
  
```python
96  image_shape = (1080., 1440.) # image_shape = (Width, Height)
```

<li>Update name in car_detection_yolo.py line 126:</li>

```python
126  out_scores, out_boxes, out_classes = predict(sess, "sh_taxi.jpg") # name = "sh_taxi.jpg"
```

</ul>

<br/>

## How does it work?

### 1. CNN output
The input image goes through a CNN, resulting in a (19,19,5,85) dimensional output. Run summary() to see whole framework architecture:
  <p align="center"><img src="https://user-images.githubusercontent.com/24521991/33063083-5dbc7662-cedc-11e7-81d7-2eac598352a8.png" width="800"></p>

### 2. Output processing
After flattening the last two dimensions, the output is a volume of shape (19, 19, 425):  
<p align="center"><img src="https://user-images.githubusercontent.com/24521991/33063496-9a98a834-cedd-11e7-80cd-daa62c999e54.png" width="400"></p>
  
<ul>
<li>Each cell in a 19x19 grid over the input image gives 425 numbers.</li>
<li>425 = 5 x 85 because each cell contains predictions for 5 boxes, corresponding to 5 anchor boxes, as seen in lecture.</li>
<li>85 = 5 + 80 where 5 is because  (pc,bx,by,bh,bw)(pc,bx,by,bh,bw)  has 5 numbers, and and 80 is the number of classes we'd like to detect</li>
</ul>

### 3. Box selection
You then select only few boxes based on:
  <ul>
  <li>Score-thresholding: throw away boxes that have detected a class with a score less than the threshold</li>
  </ul>      
  <p align="center"><img src="https://user-images.githubusercontent.com/24521991/33063777-6e849428-cede-11e7-8aaa-545d84c92a8b.png" width="400"></p>

  <ul>
  <li>Non-max suppression: Compute the Intersection over Union and avoid selecting overlapping boxes</li>
  </ul>
  <p align="center"><img src="https://user-images.githubusercontent.com/24521991/33065478-ed57eef8-cee3-11e7-8c96-9a67d63debc7.png" width="400"></p>

### 4. Final output
This gives you YOLO's final output:
<p align="center"><img src="https://user-images.githubusercontent.com/24521991/33065240-42b8b860-cee3-11e7-9cef-b219a932d1df.png" width="500"></p>
