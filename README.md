# Final Project - Video Magnification for  Segmentation

## Proposal

### Introduction
<!---
%You can use the [editor on GitHub](https://github.com/jayerfernandes/CS766/edit/master/README.md) to maintain and preview the content %for your website in Markdown files.
-->

Lenses are the most important part of almost every optical system and there has been a huge thrust in the development of millimeter/sub-millimeter scale lenses (called microlenses) in order to miniaturize optical systems. Two important areas of research are the ability to tune the focal length of the lens in real time and implementation of the lens on a flexible substrate. Our lab has been looking at combining these two areas into a single implementation.

### Problem Statement

The two figures on the left show a liquid microlens implemented on a flexible substrate. A liquid microlens consists of two immiscible liquids with different dielectric constants held in a chamber – one liquid acts as the lens and is centered over a set of electrodes, while the other liquid surrounds the lens in the chamber and exerts a force on the lens when a voltage is applied. The boundary of this lens was manually estimated and marked in red. These lenses are general actuated with an AC voltage signal in order to extend the lifetime of the lens as well as to take advantage of both electrowetting and dielectrophoretic effects.

![Image of Lens 1](https://uwmadison.box.com/s/8mocg0u667nsaw21e3tu0wf1mfyps8nd)

<a href="https://uwmadison.box.com/s/8mocg0u667nsaw21e3tu0wf1mfyps8nd"><img src="https://uwmadison.box.com/s/8mocg0u667nsaw21e3tu0wf1mfyps8nd" style="width: 500px; max-width: 100%; height: auto" title="Click for the larger version." /></a>

The proposed problem is that of the automatic detection of the boundary/edge of the lens while focusing on an image. 

As shown in the image on the left, this is made difficult by the fact that both the lens and the substrate are transparent and there is no clear variation in the intensity on which to base the edge on.

This would be a useful tool in order to determine the diameter of the lens with respect to the tuning parameters such as applied frequency and voltage while focusing on an image. The knowledge of the lens diameter, along with information about the contact angle of the liquid lens with the substrate, allows us to determine additional lens metrics.

### Ideas For Arriving at a Possible Solution

Since the lens is actuated with a frequency dependent signal, it is possible to actuate the signal with a low/high frequency voltage wave with a small amplitude and look at techniques that magnify the motion of the low frequency components. Eulerian Video Magnification or Phase based Video Processing schemes are a tentative starting approach for trying to determine the edge of the lens from a video. The frequency dependent actuation leads to a perturbation of the lens boundary that is not easily visible to the naked eye. These motions can be extracted,  amplified and averaged to determine the exact position of the boundary and the process is repeated for different voltage steps.

### Relevance/Significance of the Problem

Determining the boundary of the lens allows us to form a correlation between the diameter and the focus of the lens versus the applied voltage and frequency, which would be useful for the optimization of the lens. This would be a useful tool for my lab as well as for other researchers in the liquid lens community. 

### State of the Art

Motion processing algorithms have been applied to various scenarios such as figuring out pulse rates from miniscule hand and face deformations and color fluctuations, but I have not come across it being used to determine the edge of a liquid lens. There is commercially available software to determine the contact angle of a liquid lens, but this also requires manual intervention.



<!---
Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/jayerfernandes/CS766/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
-->
