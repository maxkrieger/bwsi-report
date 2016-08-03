---
layout: default
---

<div class="centered-text">
    <h1 class="title-huge">
        Project Report
    </h1>
    <h2 class="title-large">
        BeaverWorks Summer Institute
    </h2>

    <hr/>
</div>

<div style="margin-top: 30px" class="wrapper" markdown="1">

# I. **Week 2:** Perception

## Blob detection

To perform blob detection, we used a pipeline of computer vision
operations:

|#| Operation | Output Image |
|-|:--|:--:|
|1|Convert image to HSV colorspace|placeholder|
|2|Color thresholding|placeholder|
|3|Blurring and erosion|placeholder|
|4|Finding contours|placeholder|
|5|Polygon approximation|placeholder|
|6|Find centroid|placeholder|

# II. **Week 3:** Localization and Mapping

## Potential Fields

### **Finding Gradients**

The potential of an obstacle particle is defined as the inverse of
the distance between the car and the obstacle.

$$ U_{\text{obstacle}} = \frac{1}{\| p_{\text{car}} - p_{\text{obstacle}} \|}
= \frac{1}{\sqrt{(x_c - x_o)^2 + (y_c - y_o)^2}} $$

To determine the direction that points down the potential surface, we
find the gradients of the potential with respect to \\(x_c\\) and \\(y_c\\).

$$ \frac{\partial U_{\text{obstacle}}}{\partial x_c} =
\frac{\partial}{\partial x_c} \frac{1}{\sqrt{(x_c - x_o)^2 + (y_c - y_o)^2}} =
- \frac{x_c - x_o}{((x_c - x_o)^2 + (y_c - y_o)^2)^{3/2}}$$

And by symmetry:

$$ \frac{\partial U_{\text{obstacle}}}{\partial y_c} =
\frac{\partial}{\partial y_c} \frac{1}{\sqrt{(x_c - x_o)^2 + (y_c - y_o)^2}} =
- \frac{y_c - y_o}{((x_c - x_o)^2 + (y_c - y_o)^2)^{3/2}}$$

To make an informed driving decision, every gradient from each particle
is summed.

$$ \frac{\partial U_{\text{total}}}{\partial x_c} =
\sum \frac{\partial U_i}{\partial x_c} $$

### **Applying Gradients**

We decided to make the car's steering angle proportional to the potential gradient
with respect to x:

$$\text{steer} = k \frac{\partial U_{\text{total}}}{\partial x_c}$$

For speed, we first created an instantaneous speed update, computed as:

$$s_i = k \Big\| \Big\langle \frac{\partial U_{\text{total}}}{\partial x_c},
\frac{\partial U_{\text{total}}}{\partial y_c} \Big\rangle \Big\|
\text{sign}\Big( \frac{\partial U_{\text{total}}}{\partial y_c}\Big)$$

Instead of feeding this speed update directly to the car, we add it to
a cumulative speed variable that simulates momentum. This speed
is computed with this update rule:

$$\text{let } s_m = \mu s_m + \alpha s_i$$

\\(\mu\\) is responsible for decaying the speed with momentum over time,
comparable to friction. Values of \\(\mu\\) should be close to, but less than
one. Letting \\(\mu = 0.95\\) worked well for us.

\\(\alpha\\) determines how much influence the instanteous speed update should have
in increasing or decreasing the speed with momentum. We picked \\(\alpha = 0.06\\).

The animated figure below shows a 2D plot of the LiDAR data on the left and a
3D surface plot of the potential field on the right. This data was taken from a
run through the obstacle course.

<video controls>
    <source src="http://bwsi-report-media.s3.amazonaws.com/3d_pf_viz.mp4" type="video/mp4">
    Your browser doesn't support HTML5 video.
</video>

**Figure 1:** _(Left)_ LiDAR datapoints mapped to rectangular coordinates.
The car is at \\((0, 0)\\). _(Right)_ a surface plot of the potential field
where height represents the magnitude of the potential.

{% include plots/lidar2D.html %}
{% include plots/potfield3D.html %}

<script>


</script>

</div>