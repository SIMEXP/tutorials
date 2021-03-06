In construction, need examples (in a notebook?)

Imagine you are dealing with a huge dataset, for example uk biobank contains thousands of experimentation :
each experimentation has a certain number of patients -> each patient has few bolds scans -> one fmri scan is about ~200MB
If you want to fit all the bank into the memory, you would need terrabytes of RAM ! This is not feasible nowadays (maybe in the future with [kernel learning](https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.67.9520&rep=rep1&type=pdf) :P)
Typically in deep learning, you will use [SGD](https://scikit-learn.org/stable/modules/sgd.html) to train your model batch per batch.
The trick to manage this amount of data is to load the batch data on the fly, during the training.
This can be done by using the (fit_generator)[https://keras.io/models/model/#fit_generator] from keras, and a data generator (the object [keras.util.sequence](https://keras.io/utils/))

So now let's say you have a batch of 32 nifti files which is ~1G in RAM.
Reading the nifti image one by one inside a loop, without any optimization can make your data generator longer than the fitting !
This can be seen with tensorflow for example, where sometimes you would use 1 cpu (for the data loading) 
and all the cpus for the fitting.
Or the GPU would be used just 10% of the time, because your data_loading takes 90% to do.
https://blog.sopticek.net/2017/06/03/concurrent-and-parallel-programming-in-python-part-2/
https://aws.amazon.com/blogs/machine-learning/parallelizing-across-multiple-cpu-gpus-to-speed-up-deep-learning-inference-at-the-edge/

thread vs multiprocesses: 
I/O bound (downloads, read/write disk, ) => threads
cpu bound (data management, computation) => multiprocessing
https://www.quantstart.com/articles/Parallelising-Python-with-Threading-and-Multiprocessing

pool vs process : for huge amount of processes (> thousands) and low I/O, use multiprocessing.Pool which can efficiently dispatch your tasks among the available workers.
If you have slow number of process (~tens) and intense I/O, then use multiprocessing.Process().
https://medium.com/datadriveninvestor/python-multiprocessing-pool-vs-process-comparative-analysis-6c03c5b54eec
https://www.ellicium.com/python-multiprocessing-pool-process/

good overview:
http://blog.shenwei.me/python-multiprocessing-pool-difference-between-map-apply-map_async-apply_async/

Data feeder example in DL:
https://github.com/asheshjain399/Tensormodels/blob/master/tensormodels/data_reader/data_feeder.py

(by order of complexity)
* load in float32 instead of float64 (the precision loss in macvhine learning is ridiculous compared to the gain of time). 
* use /scratch space (10% speed improvement)
* if using the gpu, load the data before fitting (`use_multiprocessing=True` for `keras.fit_generator`)
* use multiprocesing when loading your batch (one process per sample)
* reduce the loops and use SIMD instructions
