# traffic-speed-detection-using-YOLO-and-FAST-R-CNN
A single rectangular ROI is defined over the highway lanes. In a local (GUI)
implementation, OpenCV’s selectROI is used; in Kaggle, we manually specify
(x, y, w, h) after inspecting the first frame.
YOLOv8 We use a custom-trained YOLOv8 model (‘yolov8.pt‘) loaded via the
Ultralytics API. Frames are cropped to the ROI and fed to the model, which
returns bounding boxes, class labels, and confidences.
Faster R-CNN A pretrained Faster R-CNN with a ResNet-50 backbone and
FPN is loaded from TorchVision. Detections above a 0.5 confidence threshold
within the ROI are retained.
A centroid-based tracker maintains object identities across frames. For each de-
tection of class {car, motorcycle, truck}, its centroid is matched to existing

tracks via the Hungarian algorithm on pairwise Euclidean distances. New cen-
troids spawn new tracks; disappeared tracks are pruned after 5 frames. Each

track is counted exactly once.
For each tracked object, pixel displacements (∆x, ∆y) between consecutive frames
are converted to feet using the calibration 8 px horizontally = 1 ft and 20 px
vertically = 1 ft. The Euclidean distance in feet divided by frame time (1/fps)
yields speed in m/s, converted to km/h. Speeds > 30 km/h draw a red bounding
box.
