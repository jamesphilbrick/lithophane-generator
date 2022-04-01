# Lithophane Generator
Generates a lithophane STL file from an image, ready for 3d printing! For this project, I decided to jump right in with using Anaconda and Jupyter Notebook to easily manage my code and dependencies; it was a great experience and I'll definately be using these tools again! Without having to scroll a bunch, here's the end result, after printing: 

![testPrint](/images/test_print.jpg)

## Contents
1. Process Overview
2. Results
3. Improvements and Future Work
4. What I've Learned

## 1. Process Overview
The general process is: Preprocess image → Generate point cloud → Generate mesh → Export STL.

The image is first converted to black and white using the formula:
```
greyscale = 0.2989b + 0.5870g + 0.1140b
```
I believe this is known as the *luminosity method* and, interestingly, these weights are a function of the wavelengths of the colour components!. See [this page](https://www.dynamsoft.com/blog/insights/image-processing/image-processing-101-color-space-conversion/) for more information on colour space conversion.

![grayscale](/images/grayscale_output.png)

Each pixel in the image will correspond to a vertex and have some height, z, defined by
```
z = h*(1-(p/255))+d
```
where h is the height of the lithophane (thickness), p is the pixel value 0≤p≤255, and d is the depth corresponding to the brightest part of the image. This only creates the surface point cloud, and so for each point, a corresponding vertex was created with a value of z=0 to create the flat back plane. 

![height](/images/height_output.png)

To create a mesh from the point cloud, I initially experimented with the [open3d](https://pypi.org/project/open3d/) and [pyvista](https://docs.pyvista.org/) libraries which ultimately didn't work (although they did offer some very cool pointcloud and geometry visualisation functions respectively). My plan B was to build the STL file by hand, systematically defining the edges and faces of the mesh myself. This I achieved through the use of the [numpy-stl](https://pypi.org/project/numpy-stl/) library. Looking at the documentation (which in my opinion in lacking in exposition somewhat), it seems that to define a face, you don't reference the vertices themselves, but rather *their index* in the array in which they're stored! There's likely a good reason for this but I've not yet thought of what it could be. With that being said, I managed to get my script to throw out the intended STL, which I excitedly imported into blender to inspect and see if it worked; why test something in reality when you can just simulate it all?
## 2. Results :clipboard:
This is the initial render:

![render](/images/render.png)

...and as well as the result shown above, here's my cat Tom (I was trying to see how small I could make the print before running into issues; I guess an SLA printer would be better for this application).

![tomPrint](/images/tom_print.jpg)

My printer isn't perfectly set up and and I'm not yet sure what the cause for the horizontal line in the first picture is, but it seems to be working well regardless! Annoyingly I've forgotten the source, but I read that some likely good settings and parameters are:

*Lithophane Generation Settings*
* Maximum Thickness: 3mm
* Minimum Thinness: .6mm (or .2 plus your nozzle size)
* Negative: If you have the option print with a negative setting

*Slicer Settings:*
* Infill: 100%
* Perimeters: 10
* Perimeter Speeds: 35mm/s - 45mm/s
* Print Along the Y direction

## 3. Improvements and Future Work
There are definately improvements that can be made, like adding support for non-square images, improving efficiency, etc... but at the moment I'm not concerned with implimenting any changes. My goal was to create an STL lithophane and that's what I acheived; I simply won't gain much from polishing my program up (in this case, where I'm the sole end user) and I'd rather move on to other things.

Speaking of which, I'd love to use what I've learned in this process to perhaps generate a different height-map STL file from an image - one that creates images via caustics!! I honestly didn't think that something like this could be done, but I'll definately be giving it a go, following along with blog post [*Hiding Images in Plain Sight: The Physics Of Magic Windows*](https://mattferraro.dev/posts/caustics-engineering) written by the amazing Matt Ferraro. 

![caustics](/images/caustics.png)

## 4. What I've Learned
As was to be expected, this process took longer than expected. I read somewhere that engineering projects (and I assume this holds true for many other types of projects) follow the *rule of pi* which states:
```
actual completion time = π * (expected completion time)
```
which seems to hold true! I expected to be able to bash this thing out in an evening but it took about 3~4 days...

I also learned that sometimes, libraries are not better and it may be more efficient to build a solution from the ground up. Rather than looking for a way to adapt some pre-written library functions to do what you need, it may just be easier to do it yourself. 
