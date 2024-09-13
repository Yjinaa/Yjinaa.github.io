---
title: "A Protection Service Against Unauthorized AI Training, Porotect"
layout: single
permalink: /projects/porotect/
sidebar:
    nav: "projects"
---

### A Protection Service Against Unauthorized AI Training, Porotect

*POSCO AI & Big Data Academy*

*July 17, 2023 - September 22, 2023*

**Project Summary:**
Developed a defensive mechanism to prevent unauthorized replication of an artist's style using advanced image generation algorithms, such as Stable Diffusion. The goal was to protect the stylistic elements of artwork while maintaining the visual integrity of the original image.

1. **Style Transfer Model:** The goal was to ensure that the original artwork would be replicated in the style of distinct artists such as Van Gogh or Picasso, instead of retaining its original style. To achieve this, the original artwork was transformed using a style transfer model to apply the style of artists like Van Gogh or Picasso, creating a style transferred image. This transformed image is then used to guide the Stable Diffusion model to learn and replicate the style of Van Gogh or Picasso rather than the original artwork.
2. **Feature Map Comparison with the VGG Network:** After adding subtle noise to the artwork to be protected, the feature maps of the original and protected images were compared using the VGG19 network. VGG19 extracts important visual features from the images, and the loss function is minimized by comparing the differences between these feature maps to ensure minimal discrepancy between the two images.
3. **Perceptual Similarity Optimization via LPIPS**: The LPIPS algorithm was employed to optimize the perceptual similarity between the original and protected images. This ensures that the protected image appears as visually close to the original as possible, with imperceptible noise added to the image.
4. **Noise Addition and Optimization:** The noise added to the protected image is adjusted to remain imperceptible to the human eye while using the Adam optimizer to fine-tune it. During this process, the noise is controlled to prevent it from becoming too noticeable, while ensuring that the model perceives the protected image as having the style of the stylized artwork.

### Challenges in Previous Research (Glaze):

In comparison to previous work like Glaze, our approach addresses two key limitations:
1.	Excessive number of training images: According to artists’ feedback on Reddit, Glaze requires a large dataset, often more than 50 images, to achieve meaningful results. Our method significantly reduces the number of training images needed while maintaining performance and generalization.
2.	Noticeable perturbations in illustrations: When applying Glaze to illustrations, the perturbations added to the images can be visually prominent. Our method minimizes the visibility of these perturbations, ensuring that they remain imperceptible to the human eye, even in illustrations.


### Final Goal:
The final objective is to maximize the visual similarity (LPIPS) between the original and protected images, making any changes imperceptible to human observers, while minimizing the difference between the feature maps that the model uses to perceive the style. This defensive mechanism ensures that the model interprets the style as belonging to the base artist (e.g., Picasso or Van Gogh), preventing AI models from effectively learning or replicating the original artwork's style.

Below is an example of applying Protecteck using Picasso's "Weeping Woman" as the base material.

### Illustrative Breakdown:
![Architecture](/assets/img/porotect/architecture.png)
- The above slide illustrates the Protect architecture.

![Porotect Mechanism](/assets/img/porotect/porotect.png)
- The above slide explains the Protect mechanism.

![How to Protect Artworks](/assets/img/porotect/how_to_protect_artwork.png)
- The above slide provides an explanation of the two key loss functions in the Protect mechanism and shows how they are applied in practice.

<video width="300" controls>
  <source src="/assets/videos/noise_process.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

- This video shows the process of noise being added and then gradually optimized through loss adjustments, making the noise imperceptible to the human eye.

![Comparison with Glaze](/assets/img/porotect/comparison.png)
- This is a comparison with Glaze. As you can see, the result of Protect shows less visible noise compared to Glaze, and the mathematical similarity difference is very small, at just 0.004.

![Final Result](/assets/img/porotect/final_result.png)
- Final results: After training on 10 images and 9 images respectively, the results of applying both Glaze and Protect are shown. The base artwork used here is Picasso’s Weeping Woman.
