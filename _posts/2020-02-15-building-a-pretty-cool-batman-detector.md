---
title: "Building a powerful Batman detector with Tensorflow Object Detection API"
excerpt: "Training a custom object detector with Tensorflow Object Detection API and our own dataset on Google Colab"
header:
  image: /assets/images/20200215/cover.png
  caption: "Detection made with a SSD Resnet50 detector model"
tags: 
  - Computer Vision
  - Object Detection
  - Artificial Intelligence
gallery:
  - url: /assets/images/20200215/results_ok/tst_00000011.jpg
    image_path: /assets/images/20200215/results_ok/thumbs/tst_00000011.jpg
    alt: "test image"
  - url: /assets/images/20200215/results_ok/tst_00000013.jpg
    image_path: /assets/images/20200215/results_ok/thumbs/tst_00000013.jpg
    alt: "test image"
  - url: /assets/images/20200215/results_ok/tst_00000016.jpg
    image_path: /assets/images/20200215/results_ok/thumbs/tst_00000016.jpg
    alt: "test image"
  - url: /assets/images/20200215/results_ok/tst_00000020.jpg
    image_path: /assets/images/20200215/results_ok/thumbs/tst_00000020.jpg
    alt: "test image"
  - url: /assets/images/20200215/results_ok/tst_00000023.jpg
    image_path: /assets/images/20200215/results_ok/thumbs/tst_00000023.jpg
    alt: "test image"
  - url: /assets/images/20200215/results_ok/tst_00000025.jpg
    image_path: /assets/images/20200215/results_ok/thumbs/tst_00000025.jpg
    alt: "test image"
  - url: /assets/images/20200215/results_ok/tst_00000027.jpg
    image_path: /assets/images/20200215/results_ok/thumbs/tst_00000027.jpg
    alt: "test image"
  - url: /assets/images/20200215/results_ok/tst_00000028.jpg
    image_path: /assets/images/20200215/results_ok/thumbs/tst_00000028.jpg
    alt: "test image"
  - url: /assets/images/20200215/results_ok/tst_00000029.jpg
    image_path: /assets/images/20200215/results_ok/thumbs/tst_00000029.jpg
    alt: "test image"
gallery2:
  - url: /assets/images/20200215/results_so_so/tst_00000001.jpg
    image_path: /assets/images/20200215/results_so_so/thumbs/tst_00000001.jpg
    alt: "test image"
  - url: /assets/images/20200215/results_so_so/tst_00000003.jpg
    image_path: /assets/images/20200215/results_so_so/thumbs/tst_00000003.jpg
    alt: "test image"
  - url: /assets/images/20200215/results_so_so/tst_00000004.jpg
    image_path: /assets/images/20200215/results_so_so/thumbs/tst_00000004.jpg
    alt: "test image"
  - url: /assets/images/20200215/results_so_so/tst_00000005.jpg
    image_path: /assets/images/20200215/results_so_so/thumbs/tst_00000005.jpg
    alt: "test image"
  - url: /assets/images/20200215/results_so_so/tst_00000012.jpg
    image_path: /assets/images/20200215/results_so_so/thumbs/tst_00000012.jpg
    alt: "test image"
  - url: /assets/images/20200215/results_so_so/tst_00000036.jpg
    image_path: /assets/images/20200215/results_so_so/thumbs/tst_00000036.jpg
    alt: "test image"
gallery3:
  - url: /assets/images/20200215/results_ko/tst_00000021.jpg
    image_path: /assets/images/20200215/results_ko/thumbs/tst_00000021.jpg
    alt: "test image"
  - url: /assets/images/20200215/results_ko/tst_00000022.jpg
    image_path: /assets/images/20200215/results_ko/thumbs/tst_00000022.jpg
    alt: "test image"
  - url: /assets/images/20200215/results_ko/tst_00000026.jpg
    image_path: /assets/images/20200215/results_ko/thumbs/tst_00000026.jpg
    alt: "test image"
classes: wide
share: true
comments: true
---
> _Few days ago I found this [tensorflow medium blog post](https://medium.com/tensorflow/training-and-serving-a-realtime-mobile-object-detector-in-30-minutes-with-cloud-tpus-b78971cf1193) 
saying that it was possible to create your own "realtime mobile object detector in 30 minutes"._  
I was aware of ***Transfer Learning technique*** (i.e. using a model already trained on one task and re-purposing it on another yet related task). This allows us to train a _"cat vs. dog"_ image classifier reaching near 90% accuracy in less than 25 lines of code for example.  
I was more skeptical about the time they say it would take to build this detector. So I have decided to give a try by myself to see how "easy" it is in 2020 to build a custom object detector, train it and use it.  
{: .small style="text-align: justify;"}

## Gentle reminder about what is an object detector
### Where does Object Detection land in the Computer Vision area?
Computer Vision world is wide and there is a lot to say but that would be far beyond the scope of this post so here are only few examples:
{: style="text-align: justify;"}
* The well known **image classification**: is it a dog or a cat? With its multiclass equivalent: to which breed this dog belongs to?
* **Object detection**: where are the objects within the picture?
* **Semantic segmentation/Instance segmentation**: put some pixel masks on elements
* ...
{: .small style="text-align: justify;"}
And many others that are not shown below: **activity recognition**, **pose estimation** (a.k.a body parts detection: nose, ears, eyes, elbow and so on), **facial recognition**, **emotion detection** to name but a few.
{: style="text-align: justify;"}
<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20200215/cv_tasks.png" alt="CV different tasks">
  <figcaption>Some CV tasks - Source: <a href="http://cs231n.stanford.edu/slides/2017/cs231n_2017_lecture11.pdf" target="_blank">Stanford University</a></figcaption>
</figure>

### Different approaches
So far in Object Detection, there are 3 main approaches:
* [Faster R-CNN](https://arxiv.org/abs/1506.01497) and [Mask R-CNN](https://arxiv.org/abs/1703.06870)
* [Single Shot Detectors (SSD)](https://arxiv.org/abs/1512.02325)
* [YOLO (You Only Look Once)](https://arxiv.org/abs/1506.02640)
{: .small style="text-align: justify;"}
<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20200215/ssd_vs_rcnn_vs_yolo.png" alt="SSD vs. R-CNN vs. YOLO">
  <figcaption>SSD, R-CNN and YOLO principles - <a href="https://arxiv.org/pdf/1611.10012.pdf" target="_blank">SSD R-CNN source</a> and <a href="https://arxiv.org/abs/1506.02640" target="_blank">YOLO</a></figcaption>
</figure>

Main difference is that ***Single Shot Detectors (SSDs) and YOLO use a one-stage detector strategy*** whereas the ***R-CNN*** ones ***have two stages*** (some regions of intereset are first proposed and then, for each one, there is an image classification step through a CNN).  
Inference on the former is then much quicker but accuracy is often higher with the latter.  
{: style="text-align: justify;"}
In the end it is a trade-off between speed and accuracy, it depends on the business needs.  
YOLO has its own architecture whereas SSD are models built upon a backbone network that you can choose: VGG, MobileNet, etc. It leaves us with a lot of possibilities and one has to choose wisely depending on the objective.
{: style="text-align: justify;"}

There will not be more details because a lot of other people have already explained those models better than me and I have nothing more to add.  
For the rest of this post you do not need to understand precisely how it works under the hood, I just wanted to introduce those strange names because they will come back later.  
For a deeper presentation of object detection models, I have shared some links in the resources section[^1][^2][^3][^4][^5].
{: style="text-align: justify;"}


### Performance evaluation
<figure style="width: 500px; margin:0 2em 0 0;" class="align-left">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20200215/iou_sample.jpg" alt="Intersection over Union">
  <figcaption>Intersection over Union example - <a href="https://www.pyimagesearch.com/2018/05/14/a-gentle-guide-to-deep-learning-object-detection/" target="_blank">Source</a></figcaption>
</figure>
To measure model's performance, we use an evaluation metric called ***mean Average Precision (mAP)*** which is based on the ***Intersection over Union (IoU)*** across all classes in our dataset.
Keep this metric name in mind, it will come back later.
{: style="text-align: justify;"}

### Current state of the art (SOTA)
Object Detection is constantly improving. Let's see, at the time writing, the state-of-the-art results:
{: style="text-align: justify;"}
<figure style="width: 700px;" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20200215/sota_object_detection.png" alt="SOTA Object Detection leaderboard">
  <figcaption>Object Detection challenge leaderboard - <a href="https://paperswithcode.com/sota/object-detection-on-coco" target="_blank">Source</a></figcaption>
</figure>

As you can see this is not a real new topic and there were big improvements over past years (**+18% precision over last 3 years**). 
There are still regular improvements because a lot of teams in labs, universities, GAFAM are trying to beat the SOTA results. See also how there are more and more referenced initiatives with time going.
{: style="text-align: justify;"}
<div class="notice--info">
	<h4>Few comments for a better understanding:</h4>
	<ul>
		<li>COCO: it is one of the image datasets available on Internet. It stands for <b>C</b>ommon <b>O</b>bjects in <b>CO</b>ntext and is very often used as a benchmark so that we can compare models between them by measuring their <i>mAP</i> on this dataset.</li>
		<li>Detectors trained on COCO dataset are able to detect objects belonging to 80 classes whereas it is <i>only</i> 20 classes for PASCAL VOC dataset</li>
		<li>Some models are better at detecting one particular class, here the result is an overall one, meaning that it averages all classes results</li>
		<li>Best models are almost all R-CNN ones. Like I said, they are the most accurate but also the slowest ones</li>
	</ul>
</div>


### Off-the-shelf and ready-to-use models
Fortunately for us, in 2020, there are a lot of pretrained and available models that we can use ***freely***. It means that not only researchers have publicly published the architecture and sometimes 
the code but they have also shared the files corresponding to the trained detectors.  
If the thing/object we want to detect belongs to a class the model has been trained on, there is almost nothing to do apart from downloading the model and wrapping it into our app.
If you are a little bit skilled in software engineering, this is not a big deal.
{: style="text-align: justify;"}
Here is for example result I got with a pretrained model on this picture:
<figure style="width: 900px;" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20200215/bicycle_plane_inference.jpg" alt="SOTA Object Detection leaderboard">
  <figcaption>Detection made with MobileNetV2_300_COCO pretrained model</figcaption>
</figure>

>But what if we want to build a brand new detector for a specific use case? _(and that is the original purpose of this post)_

## A practical bat-case
Let's imagine we want to detect objects that are not part of the available 80 classes from COCO. As a Batman fan, this choice was pretty obvious to me.  
Trying to detect Batman in pictures might be a little bit challenging because he is an old guy (he is now more than 80 after all) and over time, he has been drawn by many different artists and sometimes there are real differences from one Batman to another.
{: style="text-align: justify;"}
<figure style="margin-top:0;" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20200215/batman_over_time.jpg" alt="Batman look over time">
  <figcaption>Batman look over time - Cover of Detective Comics No. 1000, drawn by Nicola Scott and colored by Annette Kwok</figcaption>
</figure>

If you show the previous picture to a 4 years old kid he will tell you that all are Batman, no doubt.  
But let's remind this:
{: style="text-align: justify;"}
<figure style="width: 550px; margin-top:0;" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20200215/what_computer_sees.png" alt="Human seeing vs. computer vision">
  <figcaption>What you see vs. what the computer "sees" - <a href="https://hackernoon.com/mit-6-s094-deep-learning-for-self-driving-cars-2018-lecture-4-notes-computer-vision-f591f14b3b99" target="_blank">Source: hackernoon</a></figcaption>
</figure>

So, will the machine be able to detect Batman characteristics while being able to generalize to the most Batman pictures ?  
The question is even more _relevant_ for Robin as there were 5 different Robin over time and for this character who even once was a girl for a while, the transformation is much more visible.
{: style="text-align: justify;"}
<figure style="width: 550px; margin-top:0;" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20200215/robins.jpg" alt="Robin look over time">
  <figcaption>Left to right: Dick Grayson, Jason Todd, Tim Drake, Stephanie Brown and Damian Wayne as Robin - <a href="https://astro.temple.edu/~tue96746/robin1.html" target="_blank">Source</a></figcaption>
</figure>

The _Bat symbol_ is also very different over years, can we catch it?
<figure style="width: 450px; margin-top:0;" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20200215/bat-symbol.jpg" alt="Bat symbol evolution over time">
  <figcaption>Bat symbol design evolution over time (not exhaustive)</figcaption>
</figure>

And what would be Batman without his nemesis: Joker? Let's add this character also.  
<div class="notice--success">
	<h4>So in the end, I will try to build a model able to detect 5 elements (classes):</h4>
	<ul>
		<li>Batman's face</li>
		<li>The Bat symbol</li>
		<li>Robin's face</li>
		<li>The Robin symbol</li>
		<li>Joker's face</li>
	</ul>
</div>


### Applying Transfer Learning
In 2020, there is no reason to train such a model from scratch, that would take ages to run and would need a lot of data. There are brilliant people in GAFAM labs, universities and 
so on that are working hard every day to increase precision and beat SOTA. Let's them taking care of this part of the job.
{: style="text-align: justify;"}
We can instead use their trained models and specialize them on a similar task. This works very well for images because models have been trained to recognize so many 
things and, at some point, there is no such difference in recognizing shapes and curves in an image with a cat than in an image with Batman: they are all curves, more or less.
{: style="text-align: justify;"}
The big idea behind Transfer Learning is to use reuse the first layers of the neural network because they have been trained to detect very basic things such as edges, curves, 
certain shapes. The _only thing_ we have to do is to fine tune (i.e specialize with new training) the last layers of this network so that the output will match the number of classes we want to detect (5 in our case).
{: .notice--info style="text-align: justify;"}

### Where do we start from?
The [tensorflow medium blog post](https://medium.com/tensorflow/training-and-serving-a-realtime-mobile-object-detector-in-30-minutes-with-cloud-tpus-b78971cf1193) is a good starting
point. I highly recommend this [very good step-by-step tutorial](https://tensorflow-object-detection-api-tutorial.readthedocs.io/en/latest/training.html#) and I would also suggest to 
give a look at the [notebook from the official documentation](https://github.com/tensorflow/models/blob/master/research/object_detection/object_detection_tutorial.ipynb).

### Steps to go
Here are the main steps we will have to go through:
1. Build the training and testing datasets that will be used to train our model. In other words it means: collect a lot of pictures/videos of Batman, Robin and Joker.
2. Put labels and ground truth bounding boxes on those images
3. Choose wisely the pretrained model within the model zoo
4. Train the model
5. Test it
{: .small style="text-align: justify;"}

#### Step 1: data collection
Here you can use whatever technique you want. I mainly took from Google images and from my personal comic books. The task is tedious but easy: collect as many different pictures 
as possible. The more you get the better.  
Pay attention to grab enough pictures for each class (recommendation is at least 200 per class, not always doable though) so that the model does not _learn_ always the same thing and we 
have enough samples to show for each class.
{: style="text-align: justify;"}

#### Step 2: put truth labels and bounding boxes
<figure style="width: 500px; margin:0 0 0 1em;" class="align-right">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20200215/labelimg_usage.png" alt="Defining real bounding boxes manually">
  <figcaption>Defining real bounding boxes manually</figcaption>
</figure>
Another task which is not really funny but it has to be done one way or another: draw the real bounding boxes on images. To do that, I have used this <a href="https://github.com/tzutalin/labelImg" target="_blank">LabelImg tool</a> and it works very well.
{: style="text-align: justify;"}
This tool generates annotation files with boxes coordinates (saved as XML files in PASCAL VOC format).  
_Note that this tool can also generate files for YOLO models._

#### Step 3: choose the trained model that we will fine tune
Visit the [Tensorflow Object Detection model zoo](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md) and choose one that suits your needs.  
If real time is more important than accuracy then go for SSD, otherwise you may consider using R-CNN.
{: style="text-align: justify;"}
<figure style="width: 450px; margin-top:0;" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20200215/models_choice.png" alt="Choosing model in the Zoo">
  <figcaption>Choosing model in the Zoo</figcaption>
</figure>
As I would like to deploy the model on my phone, I will go with SSD with a MobileNet backbone because MobileNet are lighter yet efficient models specially made for on-device inference.  
I will also give a try to a SSD with a Resnet backbone as it is a good trade-off with higher accuracy and still affordable latency.
{: style="text-align: justify;"}

#### Step 4: training time!
Before launching the training there are 2 left things to do:
1. As explained in the [Tensorflow Object Detection API official documentation](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/using_your_own_dataset.md),
expected input is for data is .tfrecord. We then have to convert our XML files to match the expected format.
Fortunately, in the [step-by-step guide](https://tensorflow-object-detection-api-tutorial.readthedocs.io/en/latest/training.html#), the author has already done the job and is gracefully sharing his code.
2. We have to configure the `pipeline.config` file corresponding to our chosen model in order to specify some values:
* how many classes we have to detect?
* where are train and validation datasets?
* how many learning steps?
{: .small style="text-align: justify;"}

Once this is done we can launch the training. Here you have several options:
* train on your own computer if you have GPU
* train on the cloud where you want (GCP, AWS, whatever)
* use [Google Colab](https://colab.research.google.com/) which offers you **free GPU**. Yes: free.
{: .small style="text-align: justify;"}

I went with the third option. What is really cool with Colab (apart the fact that it works fine and is free) is that you can connect your Google Drive to the environment. So basically for the training session, here is what I did:
{: style="text-align: justify;"}
* Upload my datasets (.tfrecord files), my modified pipeline.config file and another file that transforms classnames into integers somewhere on my Google Drive
* Open a new Colab environment
* Clone tensorflow models github repository, install dependencies, compile then install the API (all steps are explained in official documentation [here](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/installation.md))
* Connect my GDrive to this environment to get access to uploaded files
* Launch the training
* Save and export trained model into Tensorflow [SavedModel format](https://www.tensorflow.org/guide/saved_model)
{: .small style="text-align: justify;"}

I trained for 2000 steps for the Resnet (~4.5 hours) and 3500 steps for the MobileNet (~ 4 hours). Training dataset contains around 400 images and validation one around 80.

<figure style="width:700px; margin-top:0;" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20200215/colab_training.png" alt="Training on colab">
  <figcaption>Training made on Google Colab</figcaption>
</figure>

#### Step 5: test the model
This is where I had more troubles because Google researchers have published this API and examples with the ***1.12*** version of Tensorflow. 
At the time writing Tensorflow 2.1 has been released and it is the ***1.15*** version by default within Colab environments.  
There were some breaking changes in the API or in the core package, some elements were deprecated and I had to make some adaptations in the official example code to make it work with new functions signatures for example.
Nothing really hard when you have developer background but still one more step to deal with.
{: style="text-align: justify;"}

As the model has been saved in Tensorflow SavedModel format, it is easy to wrap it into a class that will load it, keep a reference on the Session and expose a _predict_ method:
```python
class MyOwnDetectionModel(object):
  def __init__(self, path_to_model_dir):
    self._session = tf.Session()

    model_dir = pathlib.Path(path_to_model_dir)/"saved_model"

    start = time.time()
    model = tf.saved_model.loader.load(self._session, [tf.saved_model.tag_constants.SERVING], str(model_dir))
    end = time.time()
    print("Loading the model took {:.5} seconds".format(end - start))

    self._model = model.signature_def['serving_default']

    # Here we have to transform all TensorInfo from model.outputs into tf.Tensor
    # It could be good to automagically transform inputs/outpus information which are of type ParsedTensorInfo into TensorInfo
    # but so far I have not found an easy way to do that so for the moment it is hardcoded
    # See https://www.tensorflow.org/api_docs/python/tf/compat/v1/Session#run
    fetches = {}
    t_names = ['detection_multiclass_scores', 'detection_classes', 'num_detections', 'detection_boxes', 'raw_detection_boxes', 'detection_scores', 'raw_detection_scores']
    for tensor_name in t_names:
      # Pay attention to the ':0' that is added to tensor key name, this string is added to each tensor name in signature definitions
      fetches[tensor_name] = self._session.graph.get_tensor_by_name(tensor_name + ':0')
    
    self._fetches = fetches

  def predict(self, image):
    """
    :param image: (ndarray) the image as an array of shape [1, width, height, 3]
    """
    start = time.time()
    output_dict = self._session.run(self._fetches, feed_dict={'image_tensor:0': image})
    end = time.time()
    print("Inference for this image took {:.5} seconds".format(end - start))
    return output_dict
```

## Final results
Below is a gallery of pictures that went through the SSD Resnet50 detector model (click on image to zoom in):
{% include gallery caption="Detections with **SSD Resnet50 detector model**" %}

<div class="notice--info">
	<h4>Few comments regarding those results:</h4>
	<ul>
		<li>MobileNet was not able to reach this precision and missed most of the Batman pictures.</li>
		<li>Robin was often missed, probably because I had less Robin pictures and most of them were Damian Wayne or Tim Drake. It is interesting to note that Stephanie Brown was not detected as Robin (in 3rd picture) because the model has never seen her!</li>
		<li>Joker was, sadly, almost never found. I had even less pictures of Joker to show to the model, that is not a big surprise to me</li>
	</ul>
</div>

Those results below are not bad but they could have been better (Robin or Joker detection for example). Results are sometimes funny also: see how other characters are classified as _Robin_, for example Green Lantern with his mask. See also how Batman howl is mistaken with the Bat symbol:
{: style="text-align: justify;"}
{% include gallery id="gallery2" caption="Not so perfect detections with **SSD Resnet50 detector model**" %}

Finally, no AI being perfect, here are some pictures the model in which was not able to detect something:
{: style="text-align: justify;"}
{% include gallery id="gallery3" caption="No detections with **SSD Resnet50 detector model**" %}

## BONUS: realtime Batman Detector mobile app
Once converted into Tensorflow Lite format optimized to run low-latency, I made small changes to the [Tensorflow Object Detection Android demo](https://github.com/tensorflow/examples/tree/master/lite/examples/object_detection/android),
mainly to use my model and to build a _darker_ theme.  
Here is a video of the app running on my phone and tried on several stuff at home: my son's toy, a big DC Comics picture and even on one of my comic book.  
{: style="text-align: justify;"}

<video id="b" controls preload muted>
  <source src="{{ site.url }}{{ site.baseurl }}/assets/videos/20200215/bat_detector.ogv" type="video/ogg">
  <source src="{{ site.url }}{{ site.baseurl }}/assets/videos/20200215/bat_detector.webm" type="video/webm">
  <source src="{{ site.url }}{{ site.baseurl }}/assets/videos/20200215/bat_detector.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
If the video does not start for any reason, here is the <a href="{{ site.url }}{{ site.baseurl }}/assets/videos/20200215/bat_detector.mp4" target="_blank">link to download it</a> (~10Mb).
{: .small style="text-align: justify;"}

---
<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20200215/results_ok/tst_00000035.jpg" alt="Batmen Detection">
  <figcaption>Batmen detection with Resnet50 SSD</figcaption>
</figure>
## WRAP-UP
We have **discovered the main principles of Tensorflow Object Detection API** and what are the steps to build and train a custom object detector.  
Good surprise is that it is quite easy to build such a tool in a _small amount_ of time. It took me much more time to collect and label all data than to actually train the model. In the
original blog post, they said 30 minutes but it was because the datasets were ready to use. If you want to build a performant detector you will have to be very patient.  
This is not a surprise and a lot of DataScientists would tell you: data collection and data cleaning is almost 80% of the time you will spend on the job!
{: style="text-align: justify;"}

As usual, here are some possible further improvements:
* for sure, keep collecting pictures of Robin & Joker to reach the same level of precision than for Batman
* give a try to a R-CNN model, it could be able to detect them
{: style="text-align: justify;"}

Thanks for reading.  
Author: nidragedd
{: .small style="text-align: right;"}

---
[^1]: [towardsdatascience: review of what is a Single Shot Detector](https://towardsdatascience.com/review-ssd-single-shot-detector-object-detection-851a94607d11)
[^2]: [YOLO slideshare presentation](https://www.slideshare.net/TaegyunJeon1/pr12-you-only-look-once-yolo-unified-realtime-object-detection)
[^3]: [pyimagesearch: object detection with SSD300](https://www.pyimagesearch.com/2017/09/11/object-detection-with-deep-learning-and-opencv)
[^4]: [pyimagesearch: object detection with YOLO](https://www.pyimagesearch.com/2018/11/12/yolo-object-detection-with-opencv)
[^5]: [Another medium post on SSD multibox detection](https://medium.com/@jonathan_hui/ssd-object-detection-single-shot-multibox-detector-for-real-time-processing-9bd8deac0e06)
