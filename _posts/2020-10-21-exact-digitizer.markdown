---
layout:     post
title:      "Exact Digitizer"
subtitle:   "A MATLAB code for digitizing contour maps by color matching in the YUV colorspace"
date:       2020-10-21 12:00:00
author:     "__restrict"
header-img: "img/home-bg.jpg"
catalog: true
tags:
    - Makima Interpolation
    - RGB colorspace
    - YUV colorspace
    - UI Design
    - MATLAB
---

**Timeline**
* `2020-10-06`: released the first version
* `2020-10-19`: added a manual pixel selector
* `2020-10-21`: hosted on GitHub and rendered as Jekyll pages

Dependencies:
```
main.m
|
|-- ./borders/
|     |
|     |-- borders_documentation.m
|     |-- borders.m
|     `-- labelborders.m
|
`-- ./functions/
      |
      |-- cap_cursor.m
      |-- fuzzy_match.m
      |-- interp_cbar.m
      |-- process_img.m
      |-- refresh_axes.m
      |-- remake_cntr.m
      |-- set_locations.m
      `-- uv_distance.m
```
---

> Digitization is something you may do only if you cannot get the raw data!

<!-- Trying more examples... -->

→ The OIINK CUS Moho 2017 [Yang et al., 2017]<sup>[1]</sup>:

![yang_original](/img/in-post/post-exact-digitizer/yang_figure_6b_original.bmp)

→ Collecting user-specified pixel locations (`set_locations.m`):

![yang_locations](/img/in-post/post-exact-digitizer/yang_set_locations.png)
The collected data must be saved as `./output/yang_locations.dat`.

→ Remade contour map using auto-generated pixel locations (224 pixels selected, 100 excluded):

![yang_auto](/img/in-post/post-exact-digitizer/yang_figure_6b_auto.png)

→ Remade contour map using user-specified pixel locations (231 pixels selected, 1 excluded):

![yang_manual](/img/in-post/post-exact-digitizer/yang_figure_6b_manual.png)

[1] The dataset is available at [IRIS](https://ds.iris.edu/ds/products/emc-oiink_cus_moho2017/).

---

**Appendix**

A. Method for doing the YUV conversion (`uv_distance.m`):
```
| Y'|   |  0.299    0.587    0.114   | | R |
| U | = | -0.14713 -0.28886  0.436   | | G |
| V |   |  0.615   -0.51499 -0.10001 | | B |
```
B. List of parameters and their meanings:
```matlab
% interp_cbar.m
cbar_lim = [38, 62];  % limits for interpolating the color scale
N_levels =  64;       % the number of levels in the interpolated color scale

% main.m
X_range = [-97, -80];  % area of the real map
Y_range = [ 32,  44];  % they must define your cropped image tightly

% process_img.m
N_pixels  = ceil(N_row/15);  % pixels you'd like to skip along each axis
threshold = 20;  % threshold for excluding exceptional pixels
                 % measured as distance in the YUV colorspace

% remake_cntr.m
dl = 0.25;  % interval for presenting the remade digit map
```
**References**
* [Makima Piecewise Cubic Interpolation](https://blogs.mathworks.com/cleve/2019/04/29/makima-piecewise-cubic-interpolation/)
* [Pixel color values](https://www.mathworks.com/help/images/ref/impixel.html)
* [Define World Coordinate System of Image](https://www.mathworks.com/help/images/define-world-coordinates-using-spatial-referencing.html)
* [How to compare two colors for similarity/difference](https://stackoverflow.com/questions/9018016/how-to-compare-two-colors-for-similarity-difference)
* [Algorithm to check similarity of colors](https://stackoverflow.com/questions/5392061/algorithm-to-check-similarity-of-colors)
* [YUV Colorspace](https://softpixel.com/~cwright/programming/colorspace/yuv/)
