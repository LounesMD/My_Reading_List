# ImageNet Classification with Deep Convolutional Neural Networks

Type: Paper
Link: https://proceedings.neurips.cc/paper_files/paper/2012/file/c399862d3b9d6b76c8436e924a68c45b-Paper.pdf

# **ImageNet Classification with Deep Convolutional Neural Networks**

**Intro:** To learn complex concepts (e.g., objects from millions of images), models need large learning capacity, which requires large datasets and substantial prior knowledge. Their solution is Convolutional Neural Networks (CNNs).

**Paper Contributions:**

- Trained the largest CNN at the time.
- Developed efficient GPU code.
- Introduced new training techniques for better performance and reduced training time.
- Implemented new techniques to prevent overfitting.

**Model Architecture for better performance and training time:**

- **Nonlinear Activation:**
    - ReLU (Rectified Linear Unit) is a non-saturating nonlinearity, which contrasts with saturating nonlinearities like tanh or sigmoid. ReLU allows for faster to compute and more effective training.
- **Training on Multiple GPUs:**
    - Cross-GPU parallelization is achieved by enabling reading and writing among GPUs without routing through the host machine's memory.
    - Neurons are split between two GPUs, with communication only occurring in certain layers (specifically, the second convolutional layer and the fully connected layers).
- **Local Response Normalization:**
    - Adding local normalization before ReLU improved the results.
- **Overlapping Pooling:**
    - Overlapping pooling provided better results compared to non-overlapping pooling.

![Untitled](ImageNet%20Classification%20with%20Deep%20Convolutional%20Ne%20aeed49b9005b470f9c572152eb820f98/Untitled.png)

**Reducing Overfitting:**

- **Data Augmentation:**
    - Employed label-preserving transformations such as patch extraction (increasing the dataset size by a factor of 2048) and altering the intensities of the RGB channels using a PCA-based technique.
    - These operations are performed on the CPU during training, adding no time cost.
- **Dropout:**
    - Dropout was used in the first two fully connected layers to reduce overfitting.

**Learning Details:**

- **Weight Decay:**
    - Weight decay improved results by acting not only as a regularizer.
- **Bias Initialization:**
    - Some bias layers were initialized to 1 to give positive inputs to some ReLUs, while others were initialized to 0.

**Results:**

- The network achieved impressive results.
- As shown in of the paper’s figure, the two parts of the first layer learned different types of kernels: one part was color-agnostic, while the other was color-specific. This behavior was consistent, module GPU numbering.