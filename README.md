# PlavchanPeriodogram

Made by: Israel Andrade
[Andrade, Israel. (2023). Understanding Plavchan Algorithm: An In-Depth Analysis. 30.](https://github.com/masuta16/PlavchanPeriodgram/blob/main/Understanding%20plavchan%20algorithm.pdf)

[Open in Google Colab](https://colab.research.google.com/drive/1-aPMT-sppMF3aNsvt2KxL2prhOkLRk9G)

[![PyPI version](https://badge.fury.io/py/plavchan-periodogram.svg)](https://badge.fury.io/py/plavchan-periodogram)

The `PlavchanPeriodogram` is a Python package that provides an implementation of the Plavchan Periodogram for period analysis of time series data. This algorithm is useful for identifying periodic variations in astronomical data, such as those observed in light curves from telescopes like TESS (Transiting Exoplanet Survey Satellite).

## Installation

You can install the package using pip:

```bash
pip install PlavchanPeriodogram
```

## Usage

To use the `PlavchanPeriodogram`, you first need to import the necessary modules and create an instance of the class with your time and flux data. Here's an example of how to use it:

```python
import numpy as np
from PlavchanPeriodogram import PlavchanPeriodogram

# Assuming you have time and flux data in the form of NumPy arrays
time_data = np.array([...])
flux_data = np.array([...])

# Initialize the PlavchanPeriodogram class with time and flux data
periodogram_calculator = PlavchanPeriodogram(time_data, flux_data)

# Generate an array of trial periods to test (e.g., from 0.1 to 30.0 days)
trial_periods = np.linspace(0.1, 30.0, 10)

# Compute the periodogram for the trial periods
periodogram = periodogram_calculator.compute_periodogram(trial_periods)

# Now you have the periodogram, which contains the power values for each trial period
print(periodogram)
```

## How it Works

The `PlavchanPeriodogram` class uses the Plavchan Periodogram algorithm to find periodic variations in time series data. The main steps of the algorithm include:

1. **Phase Folding:** The time series data is folded to test for periodicity at different trial periods.

2. **Smoothing:** A box-car smoothing technique is applied to create a dynamical prior for comparison.

3. **Computing the Difference:** The squared residuals between the original flux data and the smoothed curve are calculated.

4. **Minimization:** A subset of the worst-fitting data points is used to minimize the squared residuals.

5. **Periodogram Power Calculation:** The periodogram power is calculated for each trial period based on the normalization and squared residuals.

## Contributing

If you'd like to contribute to this project, please follow the guidelines in CONTRIBUTING.md.

## License

This project is licensed under the MIT License - see the LICENSE.md file for details.

---

Here's the formula:

The Plavchan periodogram (Plavchan et al., 2008) formula is as follows:

```
periodogram_power = normalization / sum_of_squared_residuals
```

Where:
- `periodogram_power` is the calculated power value of the periodogram for the candidate period.
- `normalization` is the sum of squared errors from the worst-fit subset of data points.
- `sum_of_squared_residuals` is the sum of squared residuals from the smoothed curve.

The Plavchan periodogram is used to detect sinusoidal variations and box-shaped periodic functions equally well. It can be particularly useful in identifying periodic time-series shapes that are not well described by other algorithms' assumptions.

When rendered, it will look like this:

The Plavchan periodogram (Plavchan et al., 2008) formula is expressed as:

$periodogram\_power = \frac{normalization}{sum\_of\_squared\_residuals}$

Where:
- $periodogram\_power$ is the calculated power value of the periodogram for the candidate period.
- $normalization$ is the sum of squared errors from the worst-fit subset of data points.
- $sum\_of\_squared\_residuals$ is the sum of squared residuals from the smoothed curve.

The Plavchan periodogram is used to detect sinusoidal variations and box-shaped periodic functions equally well. It can be particularly useful in identifying periodic time-series shapes that are not well described by other algorithms' assumptions.


The Plavchan periodogram is used to detect sinusoidal variations and box-shaped periodic functions equally well. It can be particularly useful in identifying periodic time-series shapes that are not well described by other algorithms' assumptions.

### Introduction
We are discussing the Plavchan periodogram, a variation of the "phase dispersion minimization" (PDM) algorithm used for detecting periodic signals in time series data.

### Why was defined the default number of outliers equal 10?

In the implementation of the Plavchan periodogram, the parameter num_outliers is set to 10. This parameter controls the number of worst-fitting data points considered during the computation of the periodogram power. The selection of this value is a trade-off between sensitivity and computational efficiency.

The process involves comparing the data with the dynamical prior, which is generated by box-car smoothing the phased time series. By squaring the difference between the data and the prior, the algorithm quantifies the error or residuals. The sum of squared residuals is then calculated for a subset of the data points, namely the worst-fitting points.

Using a smaller value for num_outliers may increase sensitivity in detecting low signal-to-noise periodicities. However, this could also lead to increased computational burden since more data points need to be considered. On the other hand, choosing a larger value for num_outliers reduces computational cost but might sacrifice sensitivity.

By setting num_outliers to 10, a moderate compromise is made, striking a balance between sensitivity and computational efficiency in the Plavchan periodogram.


### Why is the phase box size in the Plavchan periodogram typically set to 0.05?

### Explanation

The phase box size, denoted as phase_box_size, determines the width of the phase box used for averaging the time-series data when computing the dynamical prior. In the Plavchan periodogram, a common choice for phase_box_size is 0.05.

### Rationale
Setting phase_box_size to 0.05 allows for sufficient averaging while preserving the essential features of the time series data. A value of 0.05 is often considered suitable for ground-based transit surveys with a few thousand data points, as it balances the trade-off between capturing variability and avoiding over-smoothing.

### Autoperiodogram: Determining the Number of Bins
The Autoperiodogram algorithm is employed to automatically determine the number of bins for the periodogram. It generates the trial_periods variable, which contains a set of candidate periods for exploring periodic signals in the data.

The algorithm takes as input the minimum and maximum periods to test, as well as the desired number of bins (i.e., trial periods) within that range. It evenly divides the specified period range, resulting in the trial_periods array.

To implement the Autoperiodogram algorithm in Python using the lightkurve} library, we use the following code:

```
import lightkurve as lk
import numpy as np

# Search and download the light curve data
lc = lk.search_lightcurvefile('Pi Mensae c', mission='TESS').download_all()
lc = lc[0].PDCSAP_FLUX.normalize()

# Extract time_data and flux_data from the light curve and convert to 1D NumPy arrays
time_data = lc.time.value.flatten()
flux_data = lc.flux.value.flatten()

# Autoperiodogram algorithm: Generate trial_periods array
min_period = 0.1  # Minimum trial period to test
max_period = 30.0  # Maximum trial period to test
n_periods = 1000  # Number of trial periods to generate
trial_periods = np.linspace(min_period, max_period, n_periods)

# Rest of the code for Plavchan periodogram calculation and plotting
```

The trial_periods array is essential for exploring different periods and assessing their significance in the data. By automatically determining the number of bins, the Autoperiodogram algorithm streamlines the process of identifying periodic signals, making the periodogram analysis more efficient and effective.

### Does the selection of trial periods in the Plavchan periodogram algorithm involve guessing?


### Answer
The trial periods in the Plavchan periodogram algorithm do not involve guessing. Instead, they are systematically chosen to cover a range of possible periodicities for the given time series data. In the algorithm, a set of trial periods is predefined, representing potential periodic signals that may exist in the data. The range of trial periods is often determined based on the nature of the observed phenomenon and the desired sensitivity in the search for periodic signals. These trial periods are then used to test for periodicity in the time series data, allowing the algorithm to identify the most likely period of any periodic signal present in the data. The systematic approach of selecting trial periods ensures that the algorithm comprehensively explores the periodicity landscape, making it less reliant on random guessing and more effective in detecting periodic patterns in the data.

### To implement Plavchan periodgram was used Boxcar smoothing, but what is it ?
Boxcar smoothing, also known as moving average smoothing, is a simple technique used to reduce noise and reveal trends or patterns in time-series data. It involves averaging consecutive data points within a fixed window or "box" to produce a smoothed version of the original signal.

The basic idea behind boxcar smoothing is to replace each data point with the average value of its neighboring points within the specified window. The width of the window determines the degree of smoothing. A wider window will result in more aggressive smoothing, while a narrower window will preserve more of the original data's variability.

Here's a step-by-step explanation of boxcar smoothing:

  Define the Window Size: Choose the width of the window (usually an odd integer) that determines the number of data points to include in the averaging process. For example, if the window size is set to 5, each data point will be replaced with the average of itself and the two neighboring points on each side.
```
Compute the Moving Average: Slide the window along the time series, one data point at a time,
and calculate the average of the data points within the window at each position.
For data points near the edges, where the window cannot fully fit,
adjust the window accordingly (e.g., use fewer points if near the beginning or end of the time series).

Replace Data Points: Replace each data point with its corresponding moving average value.
The result is a smoothed version of the original time series,
with reduced noise and fewer short-term fluctuations.

Boxcar smoothing is easy to implement and computationally efficient,
making it a popular choice for quick data exploration and visualization.
However, it has some limitations. The method may introduce artificial
boundaries at the edges of the time series due to the incomplete window,
and it may not be suitable for all types of data, especially if there
are abrupt changes or sharp spikes that need to be preserved.
```
Other smoothing techniques, such as Gaussian smoothing or Savitzky-Golay filtering, address some of these limitations by using weighted averaging or fitting polynomial functions to the data. The choice of smoothing technique depends on the specific characteristics of the data and the desired level of noise reduction.

