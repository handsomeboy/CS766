# Motion Magnification

## Idea
The major idea behind using motion magnification for this project follows from the following observations
* Liquid lenses are actuated using a high frequency sinusoidal input with a large magnitude
* Once the lens has moved to the desired mosition, the frequency is switched to a low frequency low input with a very low magnitude.

These two facts together allow us to think about using motion magnification in the following manner once the lens has been focused to view an object clearly
* Focus the lens on a repeating pattern/ set of patterns
* Once focused, use a low magnitude, low frequency input to actuate the device.
* Look at the pertubations (blurs) at the borders of the liquid lens in each frame.
* Use motion magnification to magnify these values and perhaps average them out.
* Run a segmentation algorthm on these perturbations

## Introduction
The goal of Eulerian Video Magnification is to magnify small temporal variations in an input video sequence in order to extract information. The Eulerian perspective is based on the properties of a voxel of fluid, such as pressure and velocity, which evolve with time. Rather than explicitly estimate the motion, this method seeks to exaggerate motion by amplifying changes at fixed positions. In the Eulerian approach to motion magnification, motions are not explicitly estimated, but rather exaggerated through the use of the same differential approximations that form the basis of optical flow algorithms.

The intuition behind amplifying image variations is that these hard to see changes occur at specific temporal frequencies and they can be augmented by using simple frequency domain filters. 

The Eulerian Magnification process is very straightforward and consists of the following steps
1. An image sequence is decomposed into different spatial frequency bands using Laplacian pyramids.
2. The time series corresponding to the value of a pixel on all levels of the pyramid are band-pass filtered to extract frequency bands of interest.
3. The extracted band-passed signals are multiplied by a magnification factor and this result is added to the original signals.
4. The magnified signals that compose the spatial pyramid are collapsed to obtain the final output.

The hardest part of the process is to find the right parameters to get the desired magnification effect. For example, one can change the size of the Laplacian pyramid, multiply the time series corresponding to the value of a pixel by different scale factors at different levels of the pyramid, or attenuate the magnification when adding the augmented band-passed signals to the original ones. The choice of the band-pass filter (e.g., the range of frequencies it passes/rejects, its order, etc.) can also influence the obtained results.

All the processing for Eulerian Video Magnification is done in the YIQ color spaces since it allows for the amplification of intensity and chromaticity independantly (if required) as opposed to the RGB color space.

### Laplacian Pyramids
We first decompose the video sequence into different spatial frequency bands. These bands might be magnified differently because 
1. They might exhibit different signal-to-noise ratios or 
2. They might contain spatial frequencies for which the linear approximation used in the motion magnification does not hold.

In the latter case, we reduce the amplification for these bands to suppress artifacts. When the goal of spatial processing is simply to
increase temporal signal-to-noise ratio by pooling multiple pixels, we spatially low-pass filter the frames of the video and downsample
them for computational efficiency. In the general case, however, we compute a full Laplacian pyramid.

This pyramid is constructed by taking the difference between adjacent levels of a Gaussian pyramid, and approximates the second derivative of the image, highlighting regions of rapid intensity change.

Each level of the Laplacian pyramid will have different spatial frequency information, as shown in the picture below. We need to upsample one of the images when computing the difference between adjacent levels of a Gaussian pyramid, since one will have a size of wxh, while the other will have (w/2)x(h/2) pixels. Since the last image in the Gaussian pyramid does not contain an adjacent image to perform the subtraction, then it just becomes the last level of the Laplacian pyramid.

<p align="center">
<img src="https://github.com/jayerfernandes/CS766/blob/master/laplacian1.jpg" width="500" >
</p>

<p align="center">
<img src="https://github.com/jayerfernandes/CS766/blob/master/laplacian2.jpg" width="500" >
</p>

<p align="center">
<img src="https://github.com/jayerfernandes/CS766/blob/master/laplacian3.jpg" width="500" >
</p>
By doing the inverse process of constructing a Laplacian pyramid we can reconstruct the original image. In other words, by upsampling and adding levels of the Laplacian pyramid we can generate the full-size picture. This reconstruction is necessary to augment videos using the Eulerian approach.

### Temporal Filtering
Temporal processing is then permormed on each spatial band. We consider the time series corresponding to the value of a pixel on all spatial levels of the Laplacian pyramid and then apply a band pass filter to this signal. The temporal processing is uniform for all spatial levels and for all pixels within each level. The choice of filter depends strongly on the application

Butterworth and IIR filters were implemented separately, since it wasn’t clear from the paper what the best filter going forward would be and both filters have broad passbands. However, I did notice that the results from the Butterworth filter were better, visually speaking. This may be just down to chance, since there is a lot of play in the filter parameters to obtain the correct results (as mentioned in the next paragraph).

The filters were defined as follwos
1. **Butterworth Filter:** Second order, Low Cutoff - 0.5 Hz, High Cutoff - 2 Hz
2. **IIR Filter:** Low Cutoff - 0.5 Hz, High Cutoff - 2 Hz
 
 
 ### Pixel Change Magnification
Once the frequency band of interest is selected, the amplification factor α, spatial frequency cutoff (specified by spatial wavelength λc, beyond which an attenuated version of α is used) and the type of attenuation for α (either force it to zero for all λ<λc or scale it down to zero linearly) is chosen. In this case, we use α = 200, λc = 100. 
The equation that bounds the amplification factor is given by (1+α)δ(t)<λ/8 where δ(t) is the displacement function utilized in motion magnification.
