[![CRAN](https://www.r-pkg.org/badges/version/kinematics)](https://cran.r-project.org/web/packages/kinematics/index.html)
[![Build Status](https://github.com/PabRod/kinematics/workflows/R-CMD-check/badge.svg?branch=main)](https://github.com/PabRod/kinematics/actions)
[![codecov](https://codecov.io/gh/PabRod/kinematics/graph/badge.svg)](https://codecov.io/gh/PabRod/kinematics)
[![codecov](https://img.shields.io/badge/lifecycle-stable-brightgreen.svg)](https://lifecycle.r-lib.org/articles/stages.html)
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.5107804.svg)](https://doi.org/10.5281/zenodo.5107804)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![fair-software.eu](https://img.shields.io/badge/fair--software.eu-%E2%97%8F%20%20%E2%97%8F%20%20%E2%97%8F%20%20%E2%97%8F%20%20%E2%97%8B-yellow)](https://fair-software.eu)
[![Downloads](https://cranlogs.r-pkg.org/badges/kinematics)](https://cran.r-project.org/web/packages/kinematics/index.html)

# Kinematics <img src="inst/img/logo.png" width="120" align="right" />

Allows analyzing time series representing two-dimensional movements.
It accepts a data frame with a time (t), horizontal (x) and vertical (y) 
coordinate as columns, and returns several dynamical properties such as speed, 
acceleration or curvature.

## Motivation and purpose

This package was born as a collaboration between a physicist and an
experimental biologist. Everything started when our department bought a
fancy device for tracking the positions of a small organism on a Petri
dish.

Long story short, the device captured several images per second, and
managed to identify the horizontal and vertical positions of the
organism under study. The output produced by the software contained time
series with values of the sampling times *t*, and the corresponding
two-dimensional positions (*x*, *y*). Unfortunately, this output also
contained some obscure and difficult to manage processed data.

We decided to write our own analysis software for these sampled
trajectories, using only the fundamental information about movement
(that is: *t*, *x* and *y*, see more on section below). This type of
data, although inspired by the particular problem of the organism’s
tracker, is completely general and applicable to any object moving in
two dimensions.

Our package returns different properties of that movement, such as the
instantaneous speeds, accelerations and curvatures.

## Impact and citation

This package has been used in the following research papers:

- Among-individual variation in the swimming behaviour of the amphipod _Gammarus pulex_ under dark and light conditions, [Science of The Total Environment, 2023](https://doi.org/10.1016/j.scitotenv.2023.162177).
- Effects of the antidepressant fluoxetine on the swimming behaviour of the amphipod _Gammarus pulex_: Comparison of short-term and long-term toxicity in the laboratory and the semi-field, [Science of The Total Environment,
2023](https://doi.org/10.1016/j.scitotenv.2023.162173).

Please cite if you use it. You can do this easily with one of these options:

- The `cite this repository` menu in the right tab.
- The [citation file](./CITATION.cff).
- If you prefer to copypaste, here you have the APA and BibTex strings:

`Rodríguez-Sánchez, P., & van den Berg, S. kinematics [Computer software]. https://github.com/PabRod/kinematics`

---

`@software{Rodriguez-Sanchez_kinematics,author = {Rodríguez-Sánchez, Pablo and van den Berg, Sanne},license = {MIT},title = {{kinematics}},url = {https://github.com/PabRod/kinematics}}`

## Installation

This is an *R* package. [*R*](https://www.r-project.org/) is required,
[*RStudio*](https://www.rstudio.com/) is recommended.

In order to use the package, we have to install it. The package is in CRAN, so 
we can install it easily by typing:

```r
install.packages("kinematics")
```

After installing, we have to attach the package as usual:

```r
library(kinematics)
```

And we are ready to go!

## Basic usage

### In a nutshell
#### Input

The input is a data frame like the one below (see subsection _Movement and how to store it_ below):

| t     | x         | y         |
|-------|-----------|-----------|
| $t_0$ | `<float>` | `<float>` |
| $t_1$ | `<float>` | `<float>` |
| $t_2$ | `<float>` | `<float>` |
| $t_3$ | `<float>` | `<float>` |
| ...   | ...       | ...       |

#### Output

When the input is fed to the `append_dynamics` function, the output extends the original data frame with all the extra dynamical information available in the package:

| t     | x         | y         |  | Velocity   | Acceleration | Curvature | Displacement |
|-------|-----------|-----------|--|------------|--------------|-----------|:-------------|
| $t_0$ | `<float>` | `<float>` |  | `<vector>` | `<vector>`   | `<float>` | `<vector>`   |
| $t_1$ | `<float>` | `<float>` |  | `<vector>` | `<vector>`   | `<float>` | `<vector>`   |
| $t_2$ | `<float>` | `<float>` |  | `<vector>` | `<vector>`   | `<float>` | `<vector>`   |
| $t_3$ | `<float>` | `<float>` |  | `<vector>` | `<vector>`   | `<float>` | `<vector>`   |
| ...   | ...       | ...       |  | ...        | ...          | ...       | ...          |


### Movement and how to store it

This is a package for studying movement. So we need a way of coding
movement into an object. This is R, so you guessed it! We’ll code
movement in the form of a data frame.

If you ask a kid to describe what movement is, probably he will trace a
curve in the air with the tips of his fingers. And he will be right,
that’s movement indeed! The apparently simple idea of “tracing a curve
with the tips of your fingers” can be abstracted to the idea of a
position that changes on time. One possible way of representing this is
by a **list of times and their corresponding positions**.

Particularly, our data frames representing movement will have three
columns:

-   The *t* column: representing time
-   The *x* column: representing the horizontal position at the time *t*
-   The *y* column: representing the vertical position at the time *t*

A minimal example could be:

```r
mov <- data.frame(t = c(0, 1, 2, 3, 4), 
                  x = c(0, 1, 2, 3, 4), 
                  y = c(0, 1, 4, 9, 16))
```

that represents five steps of what seems to be a parabolic movement:

```r
plot(mov$x, mov$y, xlab = "x", ylab = "y")
```

![](inst/img/parabolic-plot-1.png)

Notice that the insides of the data frame look like below:

```r
#>   t x  y
#> 1 0 0  0
#> 2 1 1  1
#> 3 2 2  4
#> 4 3 3  9
#> 5 4 4 16
```

where each row represents one “footprint”, that is, one snapshot of the
movement. For instance, we see that at *t*=2, the position of the
organism was (x, y) = (2,4).

### Extracting movement properties

Movement has much more properties than just position. Some examples are
speed, acceleration, curvature and displacement. The branch of physics
that deals with this kind of problems is known as
[kinematics](https://en.wikipedia.org/wiki/Kinematics). In this package,
we implemented some of the classical algorithms of kinematics. We did it
in what we think is a practical and easy-to-use fashion.

**The function `append_dynamics` is a way of extracting as much movement
properties as possible**. If we apply it to `mov`, the resulting data
frame contains much more columns:

```r
mov_analyzed <- append_dynamics(mov)

#>   t x  y vx vy   aspeed            ax ay aaccel        curv curv_radius disp_x
#> 1 0 0  0  1  0 1.000000 -3.420544e-12  2      2 2.000000000     0.50000     NA
#> 2 1 1  1  1  2 2.236068 -6.539938e-14  2      2 0.178885438     5.59017      1
#> 3 2 2  4  1  4 4.123106  3.250388e-14  2      2 0.028533603    35.04640      1
#> 4 3 3  9  1  6 6.082763  2.179979e-14  2      2 0.008886432   112.53111      1
#> 5 4 4 16  1  8 8.062258 -1.726720e-12  2      2 0.003816453   262.02338      1
#>   disp_y    adisp
#> 1     NA       NA
#> 2      1 1.414214
#> 3      3 3.162278
#> 4      5 5.099020
#> 5      7 7.071068
```

These new columns are:

- `vx`, `vy` and `aspeed`: horizontal, vertical and absolute speed.
- `ax`, `ay`, and `aaccel`: horizontal, vertical and absolute
  acceleration.
- `curv` and `curv_radius`: curvature and curvature radius.
- `disp_x`, `disp_y` and `adisp`: horizontal, vertical and absolute
  displacement (since previous time step).

#### A note about units

The units of *x* and *y* are expected to be the same (say, meters). All
the new columns derive their units from thse of *t* and *x* that is:

- Speeds’ units are: *x* unit divided by *t* unit.
- Acceleration’s units are: *x* unit divided by *t* unit squared.
- Curvature unit is 1 divided by *x* unit.
- Curvature radius’ unit is the same as *x*.
- Displacements’ units are the same as *x*.

The new columns are ready to be analyzed. For example, we can visualize
curvature and absolute speed using `ggplot2`:

```r
library(ggplot2)
ggplot(data = mov_analyzed, 
       mapping = aes(x = x, y = y, col = curv_radius, size = aspeed)) +
       geom_point() +
       scale_color_gradient(low="blue", high="red")
```

![](inst/img/ggplot-1.png)

## More realistic examples

### Parametric equation of movement

A common way of describing movement in physics is by assigning a
function of time to *x* and *y*. For instance, an Archimedean spiral is
known to follow the equations below:

<img src="https://render.githubusercontent.com/render/math?math=x(t) = t cos(t)">

<img src="https://render.githubusercontent.com/render/math?math=y(t) = t sin(t)">

We can encode it, and sample at intervals of 0.05 units of time, using
the snippet below:

```r
# Generate the times
ts <- seq(0, 10, by = 0.05)

# Calculate the positions using a function
xs <- ts * cos(ts)
ys <- ts * sin(ts)

# Store as data frame
mov <- data.frame(t = ts, 
                  x = xs, 
                  y = ys)
```

The data looks like this:

```r
plot(mov$x, mov$y, xlab = "x", ylab = "y", asp = 1)
```

![](inst/img/spiral-plot-1.png)

And repeating the analysis shown in the previous example, we can
significantly enrich the features we can see on the data. For instance,
once again, we use color to plot curvature radii and size to plot
absolute speed.

```r
mov_analyzed <- append_dynamics(mov)

ggplot(data = mov_analyzed, 
       mapping = aes(x = x, y = y, col = curv_radius, size = aspeed)) +
       geom_point(alpha = 0.5) +
       coord_fixed() +
       scale_color_gradient(low="blue", high="red")
```

![](inst/img/spiral-ggplot-1.png)

### Sampled curve

Much more interesting examples will come from actual **sampled** data,
such as the ones that our device produced. In that case, we’ll have to
load the data from whatever external source we used, and transform it to
the (t, x, y) data frame format. Sometimes, we’ll also need to clean the
data a bit (for instance, removing NAs, artifacts, … you know [the
drill](https://cengel.github.io/R-data-wrangling/)).

For pedagogical purposes, we include an example data set in this
package. The curve was traced by a macroinvertebrate called *Asella*
inside a Petri dish (no animals were harmed in the making of this
package).

This is how it looks after importing and cleaning:

```r
mov <- kinematics::example_mov

plot(mov$x, mov$y, xlab = "x", ylab = "y", asp = 1)
```
![](inst/img/load-data-1.png)

Using `append_dynamics` we can extract a lot of significant information:
And this is an example of how our analysis enriches the information
contained on it:

```r
mov_analyzed <- append_dynamics(mov)
```
And use the power of R to, for instance, make nice plots. Such as this
one that maps acceleration to color and speed to marker size.

```r
ggplot(data = mov_analyzed, 
       mapping = aes(x = x, y = y, col = aaccel, size = aspeed)) +
       geom_point(alpha = 0.1) +
       coord_fixed() +
       scale_color_gradient(low="blue", high="red")
```

![](inst/img/plot-data-1.png)

Or a histogram about accelerations:

```r
hist(mov_analyzed$aaccel, 
     breaks = 500, 
     xlab = 'Accelerations', 
     main = 'Acceleration histogram')
```

![](inst/img/hist-data-1.png)

## Citation

Please use the information below to compose your citation of this software.

```
Pablo Rodríguez-Sánchez, & Sanne J.P. van den Berg.
(2021, July 15). 
PabRod/kinematics 
Zenodo. 
http://doi.org/10.5281/zenodo.5107804
```

Different citation formats can be automatically generated from [Zenodo](https://doi.org/10.5281/zenodo.5107804).

## Authors

- [Pablo Rodríguez-Sánchez](https://pabrod.github.io)
- [Sanne J.P. van den Berg](https://www.wur.nl/en/Persons/Sanne-dr.-SJP-Sanne-van-den-Berg.htm)

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE) file for details
