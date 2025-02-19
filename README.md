# **Generative Adversarial Network (GAN) for Fashion MNIST Image Generation**

## Table of Contents
- [Project Overview](#project-overview)
- [Dataset Overview](#dataset-overview)
- [Data Preprocessing](#data-preprocessing)
- [Model Architecture](#model-architecture)
  - [Generator](#generator)
  - [Discriminator](#discriminator)
- [Model Training and Evaluation](#model-training-and-evaluation)
- [Results and Discussion](#results-and-discussion)
  - [Model Performance](#model-performance)
  - [Key Insights](#key-insights)
  - [Limitations](#limitations)
- [Conclusion](#conclusion)
- [License](#license)

## Project Overview

This project focuses on applying **Generative Adversarial Networks (GAN)** to generate fashion images using the **Fashion MNIST** dataset. The goal is to train a model capable of producing images of clothing items that resemble those in the dataset. The model uses a **Conditional GAN (cGAN)** setup, where the **generator** produces fake images from noise and labels, and the **discriminator** distinguishes real from fake images.

The project explores:
- Building a GAN architecture to generate fashion images.
- Evaluating the performance of the model using **Fréchet Inception Distance (FID)**.
- Applying adversarial training to improve image generation over time.

## Dataset Overview

The dataset used for this project is the **Fashion MNIST** dataset, which contains grayscale images of clothing items. For this project, the focus is on two categories:
- **T-shirt/top** (label 0)
- **Trouser** (label 1)

The dataset can be accessed [here](https://github.com/zalandoresearch/fashion-mnist/blob/master/README.md).

The images are **28x28 pixels**, and each image belongs to one of the specified categories. These images are used as real examples for the discriminator to distinguish from the fake images generated by the model.

## Data Preprocessing

### Steps taken to preprocess the dataset:
1. **Loading and Scaling**: The images are loaded and scaled to values between 0 and 1 to standardize the input for the model.
2. **Dataset Split**: The dataset is split into:
   - **80%** for training
   - **10%** for validation
   - **10%** for testing
3. **Label Filtering**: Only the **T-shirt/top** and **Trouser** categories are selected for this project.
4. **Reshaping**: Each image is reshaped to have one channel (28x28x1), as the model expects a single-channel input for grayscale images.

## Model Architecture

The architecture used in this project is based on a **Conditional Generative Adversarial Network (cGAN)**. The GAN architecture consists of two primary components: the **Generator** and the **Discriminator**. 

![image](https://github.com/user-attachments/assets/977fae58-89a8-4c80-955c-445a8fde565c)

### **Generator**

The generator's task is to create "fake" images from random noise and labels that resemble real images from the dataset.

1. **Input**: 
   - The generator receives two inputs: **random noise** and **labels**.
   - The **random noise** serves as a source of randomness for the generator, ensuring that different generated images are produced in each training cycle.
   - The **labels** represent the class of the image (e.g., T-shirt or Trouser), helping to condition the generator to create images for specific classes.

2. **Architecture**:
   - **Linear Layer (1024, img_size ** 2)**: This layer receives the random noise and label embeddings as input, expanding it into a large dimension (1024). The **img_size ** 2** term helps to reshape the input in a suitable format for the following layers.
   - **Linear Layer (512, 1024)**: The network further reduces the dimension to 512, learning abstract features from the original input.
   - **Linear Layer (256, 512)**: This layer further compresses the features into a lower-dimensional space (256).
   - **Linear Layer (128, 256)**: A deeper reduction is applied here, focusing on the essential features needed for image generation.
   - **Linear Layer (noise + n_labels, 128)**: This step combines the noise and label information, ensuring that the generated image corresponds to the specified label (e.g., T-shirt, Trouser).
   - **Embedding (labels)**: The label embedding integrates label-specific information into the image generation process, controlling the class of generated images.

3. **Output**:
   - The generator outputs an image shaped as a **28x28x1** grayscale image. It is trained to make this output indistinguishable from real images in the Fashion MNIST dataset.

### **Discriminator**

The discriminator's role is to distinguish between real and fake images generated by the generator. It evaluates whether an image is from the actual dataset or generated by the generator.

1. **Input**:
   - The discriminator receives both **real images** and **generated fake images** as input.
   - Additionally, the **labels** are passed to the discriminator to help it distinguish between different classes of images (i.e., it is conditioned on the same labels as the generator).

2. **Architecture**:
   - **Embedding Layer**: The labels are embedded to provide additional context to the model for classification.
   - **Linear Layer (n_labels + img_size ** 2, 512)**: This layer combines the label embedding and the image information into a single input. It expands the dimension to 512, where the model starts learning to differentiate between the two image classes.
   - **Linear Layer (512, 1024)**: A deeper layer that increases the model’s capacity to distinguish between real and fake images.
   - **Linear Layer (1024, 1024)**: Further dimensional expansion to learn complex features and patterns.
   - **Linear Layer (1024, 512)**: Reduces the dimensions back to a more compact representation.
   - **Linear Layer (512, 1)**: The final output is a single value (real or fake). The output layer uses a **sigmoid activation function** to output a value between 0 and 1, where:
     - **0** indicates a fake image.
     - **1** indicates a real image.

3. **Output**:
   - The discriminator outputs a binary classification result: **real** or **fake** based on the input image.

### **Training Process**

The **Generator** and **Discriminator** are trained together in an adversarial manner:

- **Generator**: The goal of the generator is to create images that are indistinguishable from real images, meaning that it tries to **minimize** the discriminator’s ability to differentiate between real and fake images.
- **Discriminator**: The discriminator's goal is to correctly classify real and fake images, meaning it tries to **maximize** the likelihood of distinguishing real images from fake ones.

This adversarial training continues iteratively until both the generator and discriminator reach a balance, where the generator produces highly realistic images, and the discriminator has difficulty distinguishing them from real ones.
## Results and Discussion

### Model Performance:
After training the generator and discriminator adversarially, the performance of the model was evaluated based on the **FID score**. The **FID score** measures the similarity between the generated and real images, where a lower FID indicates better image quality. 

- The generator produced images that became progressively more realistic as the training advanced.
- **Initial Results**: Early-stage training resulted in blurry and less coherent images.
- **Final Results**: After sufficient training, the generator produced images that closely resembled real images from the dataset.

### Key Insights:
1. **Training Stability**: GANs can be sensitive to hyperparameters, and proper training requires balancing the generator and discriminator’s learning rates.
2. **Latent Space**: By conditioning the generator on labels, the latent space allows for the generation of images specific to the desired categories.
3. **Fréchet Inception Distance (FID)**: FID was found to be a useful metric for evaluating image quality, showing how closely the generated images resemble real data.

### Limitations:
- **Image Quality**: While the generated images were realistic, they could be further enhanced in terms of sharpness and detail.
- **Overfitting**: Even with early stopping, overfitting occurred during the initial epochs of training, particularly with a smaller dataset.
  
## Conclusion

This project demonstrates the application of **Generative Adversarial Networks (GANs)** for image generation tasks. The model was able to generate images of T-shirts and trousers from random noise, with the final performance evaluated using the **Fréchet Inception Distance (FID)** score. Although the generated images were promising, there is room for improvement in terms of image clarity and training stability.

## License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
