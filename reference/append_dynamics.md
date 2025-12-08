# Return a data frame with extra columns with dynamical information

Return a data frame with extra columns with dynamical information

## Usage

``` r
append_dynamics(data, append.displacement = TRUE)
```

## Arguments

- data:

  A dataframe containing t, x and y

- append.displacement:

  (Optional) Set it to FALSE to not calculate displacements. Useful if
  the data is going to be resampled

## Value

A data frame including instantaneous dynamical variables, such as speed
and acceleration

## See also

[`speed`](https://pabrod.github.io/kinematics/reference/speed.md)`, `[`accel`](https://pabrod.github.io/kinematics/reference/accel.md)`, `[`append_displacement`](https://pabrod.github.io/kinematics/reference/append_displacement.md)
