# Return a dataframe with information about the time-to-time displacements

The displacement is a bit more complicated than other dynamical
variables, as it depends on the sampling frequency. If you are
subsampling, always re-run append_displacement after subsampling.

## Usage

``` r
append_displacement(data)
```

## Arguments

- data:

  A dataframe containing t, x and y

## Value

A data frame including all the dynamical information, including
displacements

## See also

[`append_dynamics`](https://pabrod.github.io/kinematics/reference/append_dynamics.md)`, `[`speed`](https://pabrod.github.io/kinematics/reference/speed.md)
