---
title: "Unauthorized AI Training Protection Service, Porotect"
layout: single
permalink: /projects/porotect/
sidebar:
    nav: "projects"
---

### Unauthorized AI Training Protection Service, Porotect

*POSCO AI & Big Data Academy*

*July 17, 2023 - September 22, 2023*

**Project Summary:**
Developed a defensive mechanism to counteract unauthorized replication of an artist's style using advanced image generation algorithms like Stable Diffusion. The goal was to protect the style information while maintaining the visual integrity of the original image.

1. **Style Transfer Model Usage:** Leveraged the distinct styles of artists like Van Gogh and Picasso as the base material. Applied a style transfer model to the digital artwork that retained the content of the original work while overlaying the style of the chosen base artwork.
2. **Feature Map Comparison using VGG Network:** Mimicked the way computer vision models interpret images by using the VGG network to align the feature maps of the original and stylized images. Adjusted the loss to ensure maximum similarity.
3. **Similarity Optimization with LPIPS:** Used the LPIPS algorithm to maximize the perceptual similarity between the original and protected artwork, ensuring that the protective layer is indistinguishable from the original to the human eye.

Through this method, the model perceives the style information as that of the base artist (e.g., Picasso or Van Gogh) rather than the original style. However, to the human eye, the changes are imperceptible.

Below is an example of applying Protecteck using Picasso's "Weeping Woman" as the base material.
