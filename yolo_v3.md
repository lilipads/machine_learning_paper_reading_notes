YOLOv3: An Incremental Improvement
4/7/2020

https://pjreddie.com/media/files/papers/YOLOv3.pdf

* 2018 work
* used for real-time object detection (predict bounding boxes). faster but less accurate than other SOTA methods. "It’s 
not as great on the COCO average AP between .5 and .95 IOU metric. But it’s very good on the old detection metric of .5 IOU."
=> so seems reasonably reliable to identify object but not as good at precise localization. Maybe it is good to be applied
to low-requirement rough pass object detection tasks or be used as a "first phase" filter. 
* (love the funny and punny writing style..) 


changes from v2:
1. score: "We do not use a softmax as we have found it is unnecessary for good performance. Instead we simply use independent logistic classifiers. During training we use binary cross-entropy loss for the class
predictions." => Use independent LR for non-exclusive multi-class classification. So for example, an object can be classified both as "truck" and "vehicle".

2. architecture (see a really good illustration at https://towardsdatascience.com/yolo-v3-object-detection-53fb7d3bfe6b).
Key is to add skip connection and upsampling (like fully convolutional network). This helps with detecting small objects.

3. instead of hardcoding, it uses K-means to determine the 9 bounding box sizes.

Sidenote:
1. interesting that the author wrote "I guess at least we know the technology is in good hands and definitely won’t be used to harvest your personal infor- mation and sell it to.... wait, you’re saying that’s exactly what it will be used for?? Oh."
And its relevance to Redmon's recent tweet: "I stopped doing CV research because I saw the impact my work was having. I loved the work but the military applications and privacy concerns eventually became impossible to ignore." (https://twitter.com/pjreddie/status/1230524770350817280?s=20)

concepts to read up later:
1. focal loss (they tried and didn't work)

reference: 
https://towardsdatascience.com/yolo-v3-object-detection-53fb7d3bfe6b <- this does a much better job of summarization of my notes.


Language modeling: given event sequence, predict next event
