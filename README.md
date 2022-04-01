# Lithophane Generator
Generates a lithophane STL file from an image, ready for 3d printing! For this project, I decided to jump right in with using Anaconda and Jupyter Notebook to easily manage my code and dependencies; it was a great experience and I'll definately be using these tools again!

## Contents :page_facing_up:
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
I believe this is known as the *luminosity method* and, interestingly, these weights are a function of the wavelengths of the colour components :thinking:. See [this page](https://www.dynamsoft.com/blog/insights/image-processing/image-processing-101-color-space-conversion/) for more information on colour space conversion.

Each pixel in the image will correspond to a vertex and have some height, z, defined by
```
z = h*(1-(p/255))+d
```
where h is the height of the lithophane (thickness), p is the pixel value 0≤p≤255, and d is the depth corresponding to the brightest part of the image. This only creates the surface point cloud, and so for each point, a corresponding vertex was created with a value of z=0 to create the flat back plane. 

To create a mesh from the point cloud, I initially experimented with the open3d and pyvista libraries which ultimately didn't work (although they did offer some very cool pointcloud and geometry visualisation functions respectively). My plan B was to build the STL file by hand, systematically defining the edges and faces of the mesh myself. This I achieved through the use of the numpy-stl library. Looking at the documentation (which in my opinion in lacking in exposition somewhat), it seems that to define a face, you don't reference the vertices themselves, but rather *their index* in the array in which they're stored. There's likely a good reason for this but I've not yet thought of what it could be
## 2. Results

## 3. Improvements and Future Work

## 4. What I've Learned
