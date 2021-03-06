# Active Contours

## Introduction
Active Contours (Snakes) are energy minimising splines that deform to fit features. Snakes lock on to nearby minima in the potential energy generated by processing an image. This energy is minimied by using an iterative gradient descent process which depends on forces derived using variational calculus and Euler Lagrange Theory. In addition, _internal_ (smoothing) forces produce tension and stiffness that constrain the behaviour of the models and _external_ forces may be specified by a supervising process or human user. For example, Gaussian smoothing followed by convolution with a gradiaent-squared operator generates a potential in which extreme correspond to edges in the original image. Over a series of iterations the force generated by this energy drives the snake into alignment with the nearest salient edge, as shown in the figure below. However, the snake must also satisfy some internal constraints – for example, it must be smooth and continuous in outline. Sometimes the user imposes additional external constraints such as attraction or repulsion.

<p align="center">
<img src="https://github.com/jayerfernandes/CS766/blob/master/activesnakes1.png?raw=true" width="500" >
</p>

Active Snakes do not try to solve the entire problem of finding salient image contours. They rely on other mechanisms to place them somewhere near the desired contour. However, even in cases where no satisfactory automatic starting mechanism exists, snakes can still be used for semiautomatic image interpretation. If an expert user pushes a snake close to an intended contour, its energy minimization will carry it the rest of the way. The minimization provides a ‘power assist’ for a person pointing to a contour feature.

Many motion tracking systems utilize active snakes to model moving objects. The main limitations of the models are
1. that they usually only incorporate edge information (ignoringother image characteristics) possibly combined with some prior expectation of shape.
2. that they must be initialised close to the feature of interest if they are to avoid being trapped by other local minima.

## Snake Energy Functionals
A snake is a parametric contour that deforms over a series of iteratinos. Each element **x**(_s,t_) along the contour therefore depends on two parameters:

s = space(curve) parameter

t = time (iteration) parameter

The contour is influenced by internal and external constraints and by image forces as outlined below:

1. **Internal Forces**: Internal constraints give the model tension and stiffness.
2. **External Forces**: External constraints come from high level sources such as human operators or automatic initialization procedures.
3. **Image Forces** Image energy is used to drive the model towards salient features such as light and dark regions, edges and terminations.

The snake can be parametrically represented as:

![equation](https://latex.codecogs.com/gif.latex?E_{snake}&space;=&space;\int_{0}^{1}E_{element}(\mathbf{x}(s))ds)                

It should be noted that while the integration implies and open-ended snake, joining the first and last elements makes the snake into a closed loop.

The above equation can be re-written in terms of the energy functionals as

![equation](https://latex.codecogs.com/gif.latex?E_{snake}&space;=&space;\int_{0}^{1}E_{internal}(\mathbf{x})ds&space;&plus;&space;\int_{0}^{1}E_{external}(\mathbf{x})ds&space;&plus;&space;\int_{0}^{1}E_{image}(\mathbf{x})ds) 


### Internal Snake Energy
The internal energy of the snake element can be written as

<img src="https://latex.codecogs.com/gif.latex?E_{internal}(\mathbf{x})=\alpha|x_{s}(s)|^2&plus;\beta|x_{ss}(s)|^2" title="E_{internal}(\mathbf{x})=\alpha|x_{s}(s)|^2+\beta|x_{ss}(s)|^2" />

The first order term controls the _tension_ and makes the snake contract like an elastic band by introducing tension. The second-order term controls the _stiffness_ and makes it resistant to bending. In other words, the parametric curve is predisposed to have constant (preferably zero) ‘velocity’ and ‘acceleration’ with respect to its parameter.

In the absence of other constraints, an active contour model simply collapses to a point
like a strip of infinitely-elastic material; however, if the ends of the model are anchored then it
forms a straight line along which the elements are evenly spaced. Adjusting the weights α(s)
and β(s) controls the relative importance of the tension and stiffness terms.



### External Snake Energy

Both automatic and manual supervision can be used to control attraction and repulsion forces that drive active contour models to or form specified features. For example, a spring like attractive force can be generated between a snake element and a point **i** in an image using the following external energy term

<img src="https://latex.codecogs.com/gif.latex?E_{external}(\mathbf{x})&space;=&space;k\left&space;|&space;\mathbf{i-x}&space;\right&space;|" title="E_{external}(\mathbf{x}) = k\left | \mathbf{i-x} \right |" />

This energy is minimal when **x=i** and it takes the value of _k_ when **i-x** = ±1.

The external energy term can also be used to make part of an image repel an active contour model by shifting the **abs(i-x)** term to the denominator as shown below

<img src="https://latex.codecogs.com/gif.latex?E_{external}(\mathbf{x})&space;=&space;\frac{k}{\left&space;|&space;\mathbf{i-x}&space;\right&space;|}" title="E_{external}(\mathbf{x}) = \frac{k}{\left | \mathbf{i-x} \right |}" />

This energy is maximal when **x=i** and it takes the value of _k_ when **i-x** = ±k. The repulsion term must be clipped as the denominator tends to zero to avoid the singularity.


### Image (Potential) Energy 
In order to make snakes useful for vision, additional energy terms need to be incorporated in order to attract them to salient features in images such as lines, edges and terminations. The total image energy can thus be expressed as a weighted combination of the three energy funcationals as shown below

![equation](https://latex.codecogs.com/gif.latex?P&space;=&space;E_{image}&space;=&space;w_{line}E_{line}&space;&plus;&space;w_{edge}E_{edge}&space;&plus;&space;w_{termination}E_{termination})
           
#### Line Functional
The simplest image functional is the  image intensity itself. Setting

![equation](http://latex.codecogs.com/gif.latex?E_{line}&space;=&space;I(x,y))

then depending on the sign of w<sub>line</sub>, the snake will be attracted either to dark lines or light lines. Subject to its other constraints, the snake will try to align itself with the lightest or darkest nearby contour.

#### Edge Functional
The simplest image functional is the  image intensity itself. Setting

<img src="https://latex.codecogs.com/gif.latex?E_{edge}&space;=&space;\left&space;|&space;\triangledown&space;I(x,y)&space;\right&space;|^2" title="E_{edge} = \left | \triangledown I(x,y) \right |^2" />

then the snake is attracted to contours with large image gradients.

#### Termination Functional
In order to find the terminations of line segments and corners, we use the curvature of level lines in a slightly smoothed image C(x,y) = G<sub>σ</sub>(x,y) * I(x,y). If the gradient direction is given by θ = tan<sup>-1</sup>(C<sub>y</sub>/C<sub>x</sub>), then the unit vectors along and perpendicular to the image gradient are given by

Tangent **n** = [Cosθ, Sinθ]

Normal **n<sub>p</sub>** = [-Sinθ, Cosθ]

The curvature of a contour C(x,y) can then be written as

![equation](http://latex.codecogs.com/gif.latex?E_{termination}&space;=&space;\int_{0}^{1}\frac{\partial&space;\theta}{\partial&space;n_{p}}ds)

![equation](http://latex.codecogs.com/gif.latex?=&space;\int_{0}^{1}\frac{\frac{\partial^2&space;C}{\partial&space;n_{p}^2}}{\frac{\partial&space;C}{\partial&space;n}}ds)

After expanding the derivatives, we get

![equation](http://latex.codecogs.com/gif.latex?E_{termination}&space;=&space;\int_{0}^{1}\frac{C_{yy}C_{x}^2&space;&plus;&space;C_{xx}C_{y}^2&space;-&space;2C_{xy}C_{x}C_{y}}{(C_{x}^2&space;&plus;&space;C_{y}^2)^{3/2}}ds)

This energy formula provides us with a simple means for attracting snakes towards corners and terminations.





## Additions to the Snake Energy Functionals

The boundaries of liquid lenses are prone to concave type deformations depending on the actuation mechanism and the electrode design of choice. As a result, the snake contour cannot easily deform and there is a need for an additional force term to push the snake to the desired boundary.

This external force term, called a _Gradient Vector Flow_ (GVF) fields, are dense vector fields derived from images by minimising an energy functional in a variational framework. The minimization is achieved by solving a pair of decoupled linear partial differential equations which diffuses the gradient vectors of a gray-level or binary edge map computed from the image.

### Gradient Vector Flow Field
The overall approach is to define a  non-irrotational external force field, which is called the gradient vector flow (GVF) field. Using a force balance condition as a starting point (rather than a variational formulation), the GVF field replaces the potential force field in the regular snake equation, defining a new snake, which is called the GVF snake. The GVF field points toward the object boundary when very near to the boundary, but varies smoothly over homogeneous image regions, extending to the image border. The main advantages of the GVF field are that it can capture a snake from a long range-from either side of the object boundary-and can force it into concave regions.

The first step is to define an edge map _f(x,y)_ derived from an image _I(x,y)_ having the property that it is larger near the image edges. Thus we have the equation

![equation](https://latex.codecogs.com/gif.latex?f(x,y)=&space;-E^{i}_{ext}(x,y))

where i = 1,2,3 or 4. The field ∇f has vectors pointing toward the edges, but it has a narrow capture range, in general. Furthermore, in homogeneous regions, I(x ,y) is constant, ∇f is zero, and therefore no information about nearby or distant edges is available.


The GVF is defined as the vector field **v**(_x,y_) = (_u(x,y), v(x,y)_) that minimises the energy functional 

<img src="https://latex.codecogs.com/gif.latex?E&space;=&space;\iint\mu&space;(u_{x}^2&space;&plus;&space;u_{y}^2&space;&plus;&space;v_{x}^2&space;&plus;&space;v_{y}^2)&space;&plus;&space;\left&space;|&space;\triangledown&space;f&space;\right&space;|^2&space;\left&space;|&space;\triangledown&space;\mathbf{v}-f&space;\right&space;|^2dxdy" title="E = \iint\mu (u_{x}^2 + u_{y}^2 + v_{x}^2 + v_{y}^2) + \left | \triangledown f \right |^2 \left | \triangledown \mathbf{v}-f \right |^2dxdy" />

This variational formulation follows a standard principle, that of making the result smooth when there is no data. In
particular, we see that when |∇f| is small, the energy is dominated by partial derivatives of the vector field, yielding a smooth field. On the other hand, when |∇f| is large, the second term dominates the integrand, and is minimized by setting v = ∇f. The parameter p is a regularization parameter governing the tradeoff between the first term and the second term. This parameter should be set according to the amount of noise present in the image (more noise, increase μ).

Using calculus of variations, it can be shown that GVF can be found by solving the following Euler equations

![equation](https://latex.codecogs.com/gif.latex?\mu\triangledown&space;^{2}u-(u-f_{x})(f_{x}^2&space;&plus;&space;f_{y}^2)&space;=&space;0)

![equation](https://latex.codecogs.com/gif.latex?\mu\triangledown&space;^{2}u-(u-f_{y})(f_{x}^2&space;&plus;&space;f_{y}^2)&space;=&space;0)

where ∇^2 is the Laplacian operator. These equations giveus another intuition behind the GVF formulation.It should be noted that in homogeneous regions, the second term of both equations and  is zero (because the gradient of f(x,y) is zero. Therefore, within these regions, u and v are each determined by Laplace’s equation. This results in a type of “filling-in’’ of information taken from the boundaries of the region.

Once the GVF vector **v**(_x,y_) has been calculate, the external potential energy force in the dynamic snake equation is replaced with this term, yielding a GVF snake, which is solved in a similar manner to the traditional snake equation i.e. dynamically and iteratively.

The figure below shows the GVF calculated for the liquid lens

<p align="center">
<img src="https://github.com/jayerfernandes/CS766/blob/master/GVF.png?raw=true" width="500" >
</p>

### Circular Shape Prior
I noticed that the active snake approach with the current energy terms took a really long time to converge to its final boundary value. In some cases, it would not converge even after 3000-4000 iterations of the dynamic algorithm. While I'm not sure about the exact cause of the lack of convergence, I suspect that it might be due to the lack of a well defined image gradient within the lens region.

In order to counter this, an addition energy term based on prior knowledge of the liquid lenses shape was added, as a result of the following observations:

* The shape of the lens remains mostly circular with minor concave artifacts i.e. low tortuosity
* The position of the lens does not vary significantly on a frame to frame basis.

Let E<sub>shape</sub> specify the energy term for the shape constraint. Also, we assume that we have n discrete points on the snake contour. We begin by choosing a set of prior points to on a morphologically processed image of the device which serves to give us a starting value of the circle radius R<sub>bar</sub> as shown below in red. The center of the prior contour can be estimated by calculating the centroid (xc, yc) of the x and y coordinates of the prior points. The shape constraint is given as follows

<p align="center">
<img src="https://github.com/jayerfernandes/CS766/blob/master/prior_choose.jpg?raw=true" width="500" >
</p>

<img src="https://latex.codecogs.com/gif.latex?E_{shape}&space;=&space;\frac{1}{2}\int_{0}^{1}(R_{x}(s,x(s))-R_{bar}(x,y)cos(2\pi&space;s))^2ds&space;&plus;&space;\frac{1}{2}\int_{0}^{1}(R_{y}(s,y(s))-R_{bar}(x,y)sin(2\pi&space;s))^2ds" title="E_{shape} = \frac{1}{2}\int_{0}^{1}(R_{x}(s,x(s))-R_{bar}(x,y)cos(2\pi s))^2ds + \frac{1}{2}\int_{0}^{1}(R_{y}(s,y(s))-R_{bar}(x,y)sin(2\pi s))^2ds" />

<img src="https://latex.codecogs.com/gif.latex?R_{x}(s,x(s))&space;=&space;x(s)-\int_{0}^{1}x(r)dr" title="R_{x}(s,x(s)) = x(s)-\int_{0}^{1}x(r)dr" />

<img src="https://latex.codecogs.com/gif.latex?R_{y}(s,y(s))&space;=&space;y(s)-\int_{0}^{1}y(r)dr" title="R_{y}(s,y(s)) = y(s)-\int_{0}^{1}y(r)dr" />

<img src="https://latex.codecogs.com/gif.latex?R_{bar}(\mathbf{x,y})=\int_{0}^{1}\sqrt{R_{x}(s,x(s))^2&space;&plus;&space;R_{y}(s,y(s))^2}" title="R_{bar}(\mathbf{x,y})=\int_{0}^{1}\sqrt{R_{x}(s,x(s))^2 + R_{y}(s,y(s))^2}" />



## Results
The image below shows the active contour progress with the GVF and shape prior. We see that the active contour segmentation is completed after **800 iterations**
<p align="center">
<img src="https://github.com/jayerfernandes/CS766/blob/master/snake_prior.gif?raw=true" width="500" >
</p>

The final segmented boundary with the prior

<p align="center">
<img src="https://github.com/jayerfernandes/CS766/blob/master/snake_shapeprior.jpg?raw=true" >
</p>

The below image shows the active contour progress with the GVF and without the shape prior. We see that the active contour segmentation is insigniiciant after **800 iterations**
<p align="center">
<img src="https://github.com/jayerfernandes/CS766/blob/master/testanimated_noprior.gif?raw=true" width="500" >
</p>

The final segmented boundary without the prior

<p align="center">
<img src="https://github.com/jayerfernandes/CS766/blob/master/snake_noprior.jpg?raw=true" >
</p>



This image shows the results of the above active contour scenario after **2500 iterations**, which is 3 times slower than withe the shape prior.

<iframe src="https://app.box.com/embed/preview/d6dgnplku6rs87lldvird9340fu11ada?theme=dark" width="640" height="360" frameborder="0" allowfullscreen webkitallowfullscreen msallowfullscreen></iframe>




## References
1. Kass, Michael, Andrew Witkin, and Demetri Terzopoulos. "Snakes: Active contour models." International journal of computer vision 1.4 (1988): 321-331.
2. Ivins, Jim, and John Porrill. "Everything you always wanted to know about snakes (but were afraid to ask)." Artificial Intelligence 2000 (1995).
3. Xu, Chenyang, and Jerry L. Prince. "Snakes, shapes, and gradient vector flow." IEEE Transactions on image processing 7.3 (1998): 359-369.
4. Ray, Nilanjan, Scott T. Acton, and Klaus Ley. "Tracking leukocytes in vivo with shape and size constrained active contours." IEEE transactions on medical imaging 21.10 (2002): 1222-1235.
5. Ray, Nilanjan, and Scott T. Acton. "Motion gradient vector flow: An external force for tracking rolling leukocytes with shape and size constrained active contours." IEEE transactions on medical Imaging 23.12 (2004): 1466-1478.
