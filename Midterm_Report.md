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
