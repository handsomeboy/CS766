# Midterm Progress Report

## Introduction
The two figures below show a liquid microlens implemented on a flexible substrate.

![Image of Lens 1](https://github.com/jayerfernandes/CS766/blob/master/prop_1.png?raw=true)
![Image of Lens 2](https://github.com/jayerfernandes/CS766/blob/master/prop_2.png?raw=true)

A liquid microlens consists of two immiscible liquids with different dielectric constants held in a chamber – one liquid acts as the lens and is centered over a set of electrodes, while the other liquid surrounds the lens in the chamber and exerts a force on the lens when a voltage is applied. The boundary of this lens was manually estimated and marked in red. These lenses are generally actuated with an AC voltage signal in order to extend the lifetime of the lens as well as to take advantage of both electrowetting and dielectrophoretic effects.
The proposed problem is that of the automatic detection of the boundary/edge of the lens in the case when both the object of interest and the background are transparent.
Since the lens is actuated with a frequency dependent signal, it is possible to actuate the signal with a low/high frequency voltage wave with a small amplitude and look at techniques that magnify the motion of the low frequency components. Eulerian Video Magnification or Phase based Video Processing schemes are a tentative approach for trying to determine the edge of the lens from a video. The frequency dependent actuation leads to a perturbation of the lens boundary that is not easily visible to the naked eye. These motions can be extracted, amplified and averaged to determine the exact position of the boundary and the process is repeated for different voltage steps.

### Steps in Eulerian Video Magnification

*	Decompose image sequence into different spatial frequency bands using Laplacian Pyramids.
*	The time series corresponding to the value of the pixel on all levels of the pyramid are band-pass filtered to extract the frequency bands of interest.
*	The extracted band-passed signals are multiplied by a magnification factor and this result is then added to the original signal.
*	The magnified signals that compose the spatial pyramid are collapsed to obtain the final output


## Initially Proposed Timeline

First Header | Second Header
------------ | -------------
February 15th | Proposal Due
February 15th – March 2nd | Understand Algorithm, Implement Gaussian Laplacian Pyramids
February 15th – March 2nd | Understand Algorithm, Implement Gaussian Laplacian Pyramids
March 2nd – March 28th | Filter Implementation, Testing of Filter Outputs, Magnification of Frequencies of interest and Laplacian Pyramid Collapse
March 29th | Midterm Progress Report Due
March 30th – April 10th | Active Snake Implementation or Geodesic Star Convexity for Segmentation
April 11th – April 20th | Play Around with different data sets for results
April 24th onwards | Presentations


## Progress Report

In brief, the following has been completed:
*	Rough implementation of Eulerian Video Magnification (there are still a few kinks to be worked out)
*	Collected data from a few lenses and tested it out on the algorithm.
*	Currently reading up on Active Contours or another method such as Star Convexity for segmentation.


### Implementation of the Gaussian Pyramids

The first step of the motion magnification algorithm is to implement either a Laplacian or a Gaussian Pyramid for every frame in the video. The Laplacian pyramid was first proposed by Burt and Adelson [1], where it was suggested that the image be sampled with Laplacian operators of many scales to create a Laplacian Pyramid.
The Laplacian Pyramid has been implemented by taking the difference between adjacent levels of a Gaussian Pyramid, which approximates the second derivative of the image, highlighting regions of rapid intensity change. 
Each level of the Laplacian pyramid will have different spatial frequency information. We also need to up-sample the smaller image when computing the difference between the the adjacent levels of the Gaussian Pyramid because of the difference in image sizes (one will have a size of m x n while the other will be (m/2) x (n/2)). As the last image in the Gaussian pyramid does not contain an adjacent image from which we can perform a subtraction, it becomes the final level of the Lapacian Pyramid.
The idea behind the Laplacian Pyramid is to recursively split off finer and finer details in order to obtain the different spatial frequency bands.

![Laplacian1](https://github.com/jayerfernandes/CS766/blob/master/laplacian1.jpg)
![Laplacian2](https://github.com/jayerfernandes/CS766/blob/master/laplacian2.jpg)
![Laplacian3](https://github.com/jayerfernandes/CS766/blob/master/laplacian3.jpg)


### Implementation of the Temporal Filters
In this step, we consider the time series corresponding to the value of a pixel at all levels of the Laplacian Pyramid. The time series is converted to the frequency domain and then passed through a filter. In the lens videos, the lens was modulated with a 1 Hz signal and I have tried to design the filters to have cutoffs at 0.5 Hz and 1.5 Hz. 
I implemented a Butterworth and IIR filter separately, since it wasn’t clear from the paper what the best filter going forward would be and both filters have broad passbands. However, I did notice that the results from the Butterworth filter were better, visually speaking. This may be just down to chance, since there is a lot of play in the filter parameters to obtain the correct results (as mentioned in the next paragraph).
Once the frequency band of interest is selected, the amplification factor α, spatial frequency cutoff (specified by spatial wavelength λc, beyond which an attenuated version of α is used) and the type of attenuation for α (either force it to zero for all λ<λc or scale it down to zero linearly) is chosen. In this case, we use α = 200, λc = 100. The equation that bounds the amplification factor is given by
(1+α)δ(t)<λ/8
where δ(t) is the displacement function utilized in motion magnification.


### Pixel Change Magnification
After extracting the frequency band of interest, the specific pixel values are amplified and then added back into the original signal.


### Image Reconstruction
After amplifying the signals, all that is left is to collapse the Laplacian pyramids into a single image per frame. We can attenuate the amplification to obtain different results, or we can low-pass filter the amplified signal to reduce effects on high frequency components of the images. An example of a reconstructed frame after motion magnification is shown below.


## Current Results and Issues
All the results are located at - https://uwmadison.box.com/s/pj81yp2p1pfs3o9szfj20utlvj8xpr2r

*	The major issue in the implementation as mentioned before is the tuning of the motion magnification parameters. Ultimately, this decides the output of the motion magnification algorithm and there is very little I can do, other than play around with the parameters, to figure out if there is a pattern in the way that the authors of the paper have chosen their values. In addition, the authors have also chosen to use different pyramid schemes with different filters and have not explained why. I have followed through with using Laplacian pyramids and butterworth/ IIR filters since it seems to work on the data that I have. I have attached videos of what I think is a successful magnification as well as failure cases.
*	Going forward, I’m a little unclear about how to go ahead with the segmentation. Active contours seems to be an interesting option, since it would be easy to implement shape priors considering the circular nature of the lens. However, it depends on the gradient and this might be challenging. Another method that I found out about was Geodesic Star Convexity. I do not know much about this at this point and I am still reading papers to understand how it works.


## References
[1] Burt, Peter, and Edward Adelson. "The Laplacian pyramid as a compact image code." IEEE Transactions on communications 31.4 (1983): 532-540.
[2]  [Wu et al 2012] "Eulerian Video Magnification for Revealing Subtle Changes in the World" 
Hao-Yu Wu, Michael Rubinstein, Eugene Shih, John Guttag, Frédo Durand and William T. Freeman ACM Trans. Graph. (Proceedings SIGGRAPH 2012) 

[3]  [Liu et al. 2005] "Motion magnification" Ce Liu, Antonio Torralba, William T. Freeman, Frédo Durand, Edward H. Adelson , ACM Trans. Graph. (Proceedings SIGGRAPH 2005) 








