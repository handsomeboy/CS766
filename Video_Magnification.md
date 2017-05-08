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
The goal of Eulerian Video Magnification is to magnify small temporal variations in an input video sequence in order to extract information. The Eulerian perspective is based on the properties of a voxel of fluid, such as pressure and velocity, which evolve with time. Rather than explicitly estimate the motion, this method seeks to exaggerate motion by amplifying changes at fixed positions. In the Eulerian approach to motion magnification, motions are not explicitly estimated, but rather exaggerated by amplifying temporal color
changes at fixed positions. We rely on the same differential approximations that form the basis of optical flow algorithms
