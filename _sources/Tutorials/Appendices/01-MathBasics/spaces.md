# Spaces

The magic of computer graphics lies in the ability to transform and manipulate objects within cartesian coordinate systems, also known as spaces for brevity. Indeed, a fundamental concept that forms the backbone of many graphics pipelines is the transformation of 3D objects to represent them in various spaces in order to bring virtual scene to be shown on the screen.

In [](transformations.md), we showed that to transform a points and vectors we can transform the basis vectors representing the starting frame, so that we can express the coordinates with respect to a new space. Building upon this foundation, it’s now interesting to look at the common spaces typically employed in the graphics pipeline and how to go from a space to another as well in order to project the 3D scenes onto a 2D surface before showing the result on the screen. 

From the initial local space where each object is defined, to the all-encompassing world space, and the camera space that offers a unique perspective, each space plays a crucial role in the intricate process of rendering. For a more extensive and detailed presentation on spaces used in computer graphics and transformations to go from one space to another, you may find valuable information in {cite}`MarschnerFundamentals2022`.


## Object space [WIP]

<br>




<br>

````{admonition} Support this project
If you found the content of this tutorial somewhat useful or interesting, please consider supporting this project by clicking on the Sponsor button below. Whether a small tip, a one-time donation, or a recurring payment, all contributions are welcome! Thank you!

```{figure} ../../../sponsor.png
:align: center
:target: https://github.com/sponsors/PAMinerva

```
````

<br>

## References
```{bibliography}
:filter: docname in docnames
```
<br>