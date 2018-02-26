---
layout: post
title: 'Image Completion with Incomplete Data'
author: seung.shin
date: 2018-02-26 15:11
tags: [vtouch, ml]
---

# Start

## Image Completion with Incomplete Data

Most of the image completion and generative models require fully-observed samples to train the network. However, as it's stated in the ambientGAN paper, obtaining high resolution samples can be very expensive or impractical for some applications.

The model in this project combines ideas from the following works:
<li>AmbientGAN</li>
<li>Globally and Locally Consistent Image Completion</li>
<br>
The AmbientGAN model enables training a generative model directly from noisy or incomplete samples. The generator in the model successfully predicts samples from the true distribution with the use of a measurement function.

On the other hand, the model in Globally and Locally Consistent Image Completion uses fully-observed samples to train the network. The completion network first uses mse loss to pre-train the weights and further uses a discriminator loss to fully train the network.

By combining ideas presented in ambientGAN and GLCIC paper, the network presented in this project learns to fill incomplete regions using only incomplete data (e.g. images randomly blocked by 28 x 28 patch).