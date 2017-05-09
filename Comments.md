# Final Comments

In this project, the major goal was to develop an automatic method/ toolbox for segmenting the boundaries of liquid lenses in order to correlate their physical properties with the focal distance and develop efficiency metrics for these lenses.

While the goal of segmentation was achieved, there is still a lot of work to be done.

1. I had initially proposed the use of video magnification in order to deal with the issues that active contours/snakes had with concave regions. However, I feel that this was countered reliably by the GVF method, thus rendering the video magnification method obsolete.
2. Segmentation of the liquid lens was achieved with the active snakes method.
3. I have tried implementing this with the lens focused on a set of patterns with limited success. I'm currently using the set of stars as an object but the dimensions of the object and the spacing in between each star seems to be very large.
