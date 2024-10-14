# SOFR Swap Curve Bootstrapping and Animation

This repository contains a Python script that bootstraps a SOFR (Secured Overnight Financing Rate) swap curve, calculates zero rates from discount factors, and animates the curve over time. The swap curve evolves over a specified date range, showing how the SOFR swap curve changes as you move from the past to the present.

## Features

- **SOFR Swap Curve Bootstrapping**: Computes discount factors and zero rates for a range of maturities (from 1 month to 2 years) using swap rates.
- **Zero Rate Calculation**: Derives zero-coupon rates from the computed discount factors for each date in the dataset.
- **Animated Swap Curve**: Animates the bootstrapped SOFR swap curve, showing the change in zero rates over time.
- **Custom Maturity Handling**: Handles custom maturities for both short-term and long-term SOFR swaps.

## Requirements

To run the project, you need the following Python libraries:

```bash
pip install numpy pandas matplotlib scipy
```

- `numpy`: For numerical operations.
- `pandas`: For handling data in DataFrames.
- `matplotlib`: For plotting and animating the swap curve.
- `scipy`: For solving equations in the bootstrapping process.

## Files

- `analytical_fianance_2.ipynb`: Jupyter Notebook with all the functions and code required to bootstrap the SOFR swap curve and animate the results.
- `README.md`: This file explains the usage and purpose of the project.

## How It Works

### 1. SOFR Swap Curve Bootstrapping

The swap curve bootstrapping process involves calculating discount factors for different maturities based on the available SOFR swap rates. For shorter maturities (less than or equal to 1 year), the discount factors are computed directly from the given rates. For longer maturities (more than 1 year), the discount factors are solved using an iterative process (`fsolve` from `scipy`), ensuring that the fixed and floating legs of the swap are balanced.

### 2. Zero Rate Calculation

Once the discount factors are computed, zero rates (continuously compounded) are derived using the formula:

$$r(T) = -\frac{\ln(D(T))}{T}$$

Where:
- $r(T)$ is the zero rate for a given maturity $T$,
- $D(T)$ is the discount factor for that maturity.

### 3. Animation of the Swap Curve

The script uses `matplotlib.animation.FuncAnimation` to create an animated plot showing the evolution of the SOFR swap curve over time. For each date in the dataset, the plot is updated with the corresponding zero rates and maturities.

## Usage

1. Clone this repository:

```bash
git clone https://github.com/yourusername/sofr-swap-curve-bootstrapping.git
cd sofr-swap-curve-bootstrapping
```

2. Install the required dependencies:

```bash
pip install -r requirements.txt
```

3. Run the script or Jupyter Notebook to perform the following tasks:
    - Bootstrap the SOFR swap curve.
    - Calculate zero rates from discount factors.
    - Animate the swap curve over time.

4. **Plot the Swap Curve**: You can plot the swap curve for a specific date by calling:

```python
plot_swap_curve(df, maturities, date='YYYY-MM-DD')
```

5. **Animate the Swap Curve**: To see the animated evolution of the swap curve, run:

```python
ani = FuncAnimation(fig, update, frames=len(df.index), interval=1000, blit=True)
plt.show()
```

## Example

```python
# Example usage for plotting the swap curve for a specific date
plot_swap_curve(df, maturities, date='2023-01-01')

# Example usage for animating the swap curve over time
ani = FuncAnimation(fig, update, frames=len(df.index), interval=1000, blit=True)
plt.show()
```

## Customization

- You can adjust the maturities or swap rates in the dataset by modifying the input `DataFrame` (`df`) or passing custom maturities.
- The animation speed can be adjusted by changing the `interval` in the `FuncAnimation` function.

## License

This project is licensed under the MIT License. See the `LICENSE` file for details.
