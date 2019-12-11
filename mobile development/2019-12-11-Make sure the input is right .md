# Make sure the input is right 
[Marc Steven](https://github.com/MarcSteven)

---
Even though the trained model was converted 100% correctly to Core ML format.
It's still possible to get the wrong predictions.
If you are not certain about the results from Core ML, it's smart to double check that your input are indeed what the model expects.
For models that take image as input, some of the things that can go wrong are :

* 1, The input image is wrong, for example it's all black. This can happen when you are using CoreML without vision, in which case you are responsible for resizing and cropping the input image yourself.
* 2,The image orientation is incorrect. You need to tell Vision about the EXIF orientation from the image file, or about the current orientation of the device if you are working on live video.
* 3, The imageCropAndScaleOption is not the same as what your model was trained with.
* 4,The image preprocessing options from the mlmodel may be wrong.



