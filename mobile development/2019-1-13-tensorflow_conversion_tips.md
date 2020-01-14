TensorFlow lets you save your models in a few different formats:
* A full graph definition, including trainning operations
* a trainning checkpoint 
* a frozen graph that is optimized for inference 
* a SavedModel object 
* a tflite file for use with TensorFlow lite
The tfcoreml converter can only convert frozen graphs to CoreML.
If your Tensorflow model is saved in a different format, you will have to convert it first.
To convert a checkpoint to a frozen graph, you can use the freeze_graph.py.[freeze graph](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/python/tools/freeze_graph.py)
