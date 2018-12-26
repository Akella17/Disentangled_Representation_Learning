# β - VAE

This is an implementation of β-variational auto-encoders (β-VAE), the state-of-the-art model for unsupervised visual disentangled representation learning. This work replicating DeepMind's papers ["β-VAE: Learning Basic Visual Concepts with a Constrained Variational Framework"](https://openreview.net/forum?id=Sy2fzU9gl) and ["Understanding disentangling in β-VAE"](https://arxiv.org/abs/1804.03599)

## What is disentanglement?

A disentangled representation can be defined as one where single latent units are sensitive to changes in single generative factors, while being relatively invariant to changes in other factors. Here a feature representation can be thought of as a coordinate system with each latent unit being one of its many axes. There are many ways to extract independent feature representations (PCA and ICA decompositions), however independence does not guarantee disentanglement. Take for instance a 2D coordinate system with generative factors as ![](https://latex.codecogs.com/gif.latex?\vec{i}) and ![](https://latex.codecogs.com/gif.latex?\vec{j}) vectors. While ![](https://latex.codecogs.com/gif.latex?3\vec{i}/_5&plus;4\vec{j}/_5) and ![](https://latex.codecogs.com/gif.latex?\vec{i}/_2&plus;\sqrt{3}\vec{j}/_2) form independent factors in 2D space, they are not disentangled, i.e., change of one latent factor will affect multiple generative factors.

## Why do we need disentanglement?

- good interpretability
- aids an RL agent to achieve better transfer performance outside of the training data distribution
- a step towards the generality and robustness of biological intelligence

### Usefulness?

- A model trained on a dataset of 3D objects might learn independent latent units sensitive to single independent data generative factors, such as object identity, position, scale, lighting or colour, thus acting as an inverse graphics model (Kulkarni et al., 2015).
- Scenarios which require knowledge transfer, where faster learning is achieved by reusing learnt representations for numerous tasks.
- zero-shot inference, where reasoning about new data is enabled by recombining previously learnt factors.


## Methodology

Follows the architecture of variational autoencoder (VAE) framework with an adjustable hyperparameter β (β = 1 corresponds to regular VAE). This hyperparameter imposes a limit on the capacity of the latent information channel and an emphasis on learning statistically independent latent factors. This, when combined with the data log likelihood maximisation objective of generative models, leads to the model acquiring the most efficient latent representation of the data, which is disentangled if the data is produced using at least some ground truth factors of variation that are independent.

![](https://github.com/Akella17/Disentangled_Representation_Learning/raw/master/dsprites/old_beta_loss.png)

The above equation forms the objective function that needs to be **maximized**. While the first term accounts for reconstruction fidelity, the second term is a measure of the KL distance of learnt posterior and Gaussian prior $p(z)$ (puts implicit independence pressure derived from the isotropic nature of Gaussian prior). Hence, higher values of β should encourage learning a disentangled representation of v. The extra pressures coming from high β values, however, may create a trade-off between reconstruction fidelity and the quality of disentanglement within the learnt latent representations.

![](https://github.com/Akella17/Disentangled_Representation_Learning/raw/master/dsprites/disentangled_loss.png)

## Dataset Description

[dSprites](https://github.com/deepmind/dsprites-dataset) is a dataset of 2D shapes procedurally generated from 6 ground truth independent latent factors. These factors are color, shape, scale, rotation, x and y positions of a sprite.

All possible combinations of these latents are present exactly once, generating N = 737280 total images.

* Color: white
* Shape: square, ellipse, heart
* Scale: 6 values linearly spaced in [0.5, 1]
* Orientation: 40 values in [0, 2 pi]
* Position X: 32 values in [0, 1]
* Position Y: 32 values in [0, 1]

![dSprites GIF](https://github.com/Akella17/Disentangled_Representation_Learning/raw/master/dsprites/dsprites.gif)

- `imgs` : (737280 x 64 x 64, uint8) Images in black and white.
- `latents_values` : (737280 x 6, float64) Values of the latent factors.
- `latents_classes` : (737280 x 6, int64) Integer index of the latent factor values. Useful as classification targets.
- `metadata` : some additional information, including the possible latent values.

## 2D shape disentaglement

Result by changing latent Z from -3.0 to 3.0 with γ=100.0 and C=20.0

Latent variables with small variances seem extracting "x", "y", "rotation" and "scale" parameters.

(This experiment is using DeepMind's [dsprite data set](https://github.com/deepmind/dsprites-dataset).)


Z  | Image                             | Parameter | Variance
---| ----------------------------------|---------- |-------
z0 | ![](disentangle_anim/anim_z0.gif) |           | 0.9216
z1 | ![](disentangle_anim/anim_z1.gif) |           | 0.9216
z2 | ![](disentangle_anim/anim_z2.gif) | Rotation  | 0.0011
z3 | ![](disentangle_anim/anim_z3.gif) | Rotation? | 0.0038
z4 | ![](disentangle_anim/anim_z4.gif) | Pos X     | 0.0002
z5 | ![](disentangle_anim/anim_z5.gif) |           | 0.9384
z6 | ![](disentangle_anim/anim_z6.gif) | Scale?    | 0.0004
z7 | ![](disentangle_anim/anim_z7.gif) |           | 0.8991
z8 | ![](disentangle_anim/anim_z8.gif) |           | 0.9483
z9 | ![](disentangle_anim/anim_z9.gif) | Pos Y     | 0.0004


## Reconstruction result

Left: original Right: reconstructed image

![](reconstr_img/org_0.png)
![](reconstr_img/reconstr_0.png)

![](reconstr_img/org_1.png)
![](reconstr_img/reconstr_1.png)

![](reconstr_img/org_2.png)
![](reconstr_img/reconstr_2.png)

![](reconstr_img/org_3.png)
![](reconstr_img/reconstr_3.png)

![](reconstr_img/org_4.png)
![](reconstr_img/reconstr_4.png)

![](reconstr_img/org_5.png)
![](reconstr_img/reconstr_5.png)

![](reconstr_img/org_7.png)
![](reconstr_img/reconstr_7.png)

![](reconstr_img/org_8.png)
![](reconstr_img/reconstr_8.png)

![](reconstr_img/org_9.png)
![](reconstr_img/reconstr_9.png)
