# Tensorflow-input-pipeline

This is an input pipeline function for Tensorflow, which uses the Dataset API, and is designed for use with semantic segmentation datasets.

I have observed that generally importing your own data into tensorflow for deep learning/machine learning problems is...well...a problem, this code aims to simplify that, and get you up and running with your deep learning projects. The code is simple and readable, so you can easily edit and extend it for your own projects.

# Augmentation Examples:
Following shows the same image, loaded with the pipeline, note the different augmentations (birghtness, contrast, saturation, cropping and flipping changes, and the masks are changed accordingly. Also, the color mask is correctly one hot encoded and each one hot map is separately shown). The example image is taken from the KITTI road segmentation dataset, the three classes are ignore, background and road.

![Exmaple](https://user-images.githubusercontent.com/4294680/44959415-13a79780-aeee-11e8-8366-0eed590b3c2d.png)

# Example use:

```python 
import matplotlib.pyplot as plt
import tensorflow as tf
from dataloader import DataLoader
import os

plt.ioff()

IMAGE_DIR_PATH = 'data/training/images'
MASK_DIR_PATH = 'data/training/masks'

# create list of PATHS
image_paths = [os.path.join(IMAGE_DIR_PATH, x) for x in os.listdir(IMAGE_DIR_PATH) if x.endswith('.png')]
mask_paths = [os.path.join(MASK_DIR_PATH, x) for x in os.listdir(MASK_DIR_PATH) if x.endswith('.png')]

# Where image_paths[0] = 'data/training/images/image_0.png' 
# And mask_paths[0] = 'data/training/masks/mask_0.png'

# Initialize the dataloader object
dataset = DataLoader(image_paths=image_paths,
                     mask_paths=mask_paths,
                     image_extension='png',
                     image_size=[256, 256],
                     crop_size=[400, 400],
                     channels=[3, 3]
                     palette=[0, 255],
                     seed=47)

# Parse the images and masks, and return the data in batches, augmented optionally.
data, init_op = dataset.data_batch(augment=True, 
                                   shuffle=True,
                                   batch_size=BATCH_SIZE,
                                   num_threads=4,
                                   buffer=60)


with tf.Session() as sess:
  # Initialize the data queue
  sess.run(init_op)
  # Evaluate the tensors
  aug_image, aug_mask = sess.run(data)
                                 
  # Do whatever you want now, like creating a feed dict and train your models,
  # You can also directly feed in the tf tensors to your models to avoid using a feed dict.

```

# Note
This code file is meant as a guide for anyone stuck at functions for loading your own data into Tensorflow, generally most problems in ML will follow the skeleton of this example, where you load image and labels (here label is just another image) -> you will preprocess this loaded data -> batch it -> return an iterator over it.

# Contributing
Ideas for extending this are welcome.
If you would like to contribute:
1. Clone the repo.
2. Create your own branch.
3. Make your changes.
4. Commit and make a pull request.
