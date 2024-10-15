# SOFR Swap Curve Bootstrapping and Animation

This repository contains a Python script that bootstraps a SOFR (Secured Overnight Financing Rate) swap curve, calculates zero rates from discount factors, and animates the curve over time. The swap curve evolves over a specified date range, showing how the SOFR swap curve changes as you move from the past to the present.

## Features

- **SOFR Swap Curve Bootstrapping**: Computes discount factors and zero rates for a range of maturities (from 1 day to 30 years) using swap rates.
- **Zero Rate Calculation**: Derives zero-coupon rates from the computed discount factors for each date in the dataset.
- **Bond Price and Yield Calculation**: Computes bond prices and yields from the discount factors.
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

- `swap-curve-bootstrapping.ipynb`: Jupyter Notebook with all the functions and code required to bootstrap the SOFR swap curve and animate the results.
- `README.md`: This file explains the usage and purpose of the project.

## How It Works

### 1. SOFR Swap Curve Bootstrapping

The swap curve bootstrapping process involves calculating discount factors for different maturities based on the available SOFR swap rates. For shorter maturities (less than or equal to 1 year), the discount factors are computed directly from the given rates, such as the Federal Funds Rate, SOFR Index, and Treasury Bill rates.

For longer maturities (more than 1 year), discount factors are bootstrapped using an iterative process. At each maturity, the swap rate $S(T_n)$ is used to balance the fixed and floating legs of the swap. The formula to calculate the discount factor $D(T_n)$ for each maturity is as follows:

$$
D(T_n) = \frac{1 - S(T_n) \sum_{i=1}^{n-1} D(T_i) \Delta t}{1 + S(T_n) \Delta t}
$$

Where:
- $D(T_n)$ is the discount factor at time $T_n$,
- $S(T_n)$ is the swap rate for the maturity $T_n$,
- $D(T_i)$ are the previously computed discount factors for earlier maturities,
- $\Delta t$ is the time between fixed payments (usually 1 year for simplicity).

This ensures that the fixed leg (computed using known discount factors) is equal to the floating leg at each maturity.

### 2. Zero Rate Calculation

Once the discount factors are computed, zero rates (continuously compounded) are derived using the following formula:

$$
r(T) = -\frac{\ln(D(T))}{T}
$$

Where:
- $r(T)$ is the zero rate for a given maturity $T$,
- $D(T)$ is the discount factor for that maturity,
- $T$ is the time to maturity.

This formula converts the discount factor into a continuously compounded zero-coupon rate.

### 3. Bond Prices and Yields Calculation

Using the computed discount factors, bond prices are derived for each maturity. The price of a zero-coupon bond for a given maturity $T$ is simply the inverse of the discount factor:

$$
\text{Bond Price}(T) = \frac{1}{D(T)}
$$

Bond yields are then calculated based on the discount factors. The yield to maturity (YTM) for a zero-coupon bond is given by:

$$
\text{Yield}(T) = \left( \frac{1}{D(T)} - 1 \right) \times \frac{1}{T} \times 100
$$

Where:
- $\text{Yield}(T)$ is the yield to maturity for a given bond,
- $D(T)$ is the discount factor for the maturity $T$,
- $T$ is the time to maturity.

### 4. Iteration Over Dates

For each date in the dataset, the bootstrapping process is applied, and the results (discount factors, bond prices, and bond yields) are stored in a DataFrame. This allows for the tracking of changes in the SOFR curve and related bond market metrics over time.

The process results in three main outputs for each date:
1. **Discount Factors** for each maturity.
2. **Bond Prices** for each zero-coupon bond.
3. **Bond Yields** for each maturity.

These results are stored in DataFrames for further analysis or visualization.

### 5. Animation of the Swap Curve

The script uses `matplotlib.animation.FuncAnimation` to create an animated plot showing the evolution of the SOFR swap curve over time. For each date in the dataset, the plot is updated with the corresponding zero rates and maturities.

## Usage

1. Clone this repository:

```bash
git clone https://github.com/yourusername/swap-curve-bootstrapping.git
cd swap-curve-bootstrapping
```

2. Install the required dependencies:

```bash
pip install -r requirements.txt
```

3. Run the script or Jupyter Notebook to perform the following tasks:
    - Bootstrap the SOFR swap curve.
    - Calculate zero rates from discount factors.
    - Animate the swap curve over time.
  
4. **Plot a Curve**: You can plot any curve for a specific date by calling:

```python
plot_curve(df, maturities, date='YYYY-MM-DD')
```
This will plot the a curve for different maturities at a given date.

6. **Animate the Swap Curve**: To see the animated evolution of the swap curve, run:

```python
ani = FuncAnimation(fig, update, frames=len(df.index), interval=1000, blit=True)
plt.show()
```

## Example

```python
# Example usage for plotting the swap curve for a specific date
plot_curve(bond_yields_df, bond_yields_df.columns.tolist(), '2023-12-29', 'Bootstrapped Swap Curve')

# Another example usage for ploting the bond price for a specific date.
plot_curve(bond_prices_df, bond_prices_df.columns.tolist(), '2023-12-29', 'Bond Prices Curve')


# Example usage for animating the swap curve over time
animate_data(bond_yields_df,
                maturities,
                'Bootstrapped Swap Curve',
                'Maturity (Years)',
                'Zero Rate (%)',
                save=True,
                filename='bootstrapped_swap_curve.gif')


# Another example usage for animating the bond price over time of instruments:
animate_data(bond_prices_df,
                maturities,
                'Bond Prices Curve',
                'Maturity (Years)',
                'Bond Price',
                save=True,
                filename='bond_prices_curve.gif')

# Example of two curves on the same animation over time:
animate_data(bond_yields_df,
                maturities,
                'Bootstrapped Swap Curve',
                'Maturity (Years)',
                'Zero Rate (%)',
                save=True,
                filename='bootstrapped_swap_curve.gif',
                secondary_data=rate_df,
                secondary_label='Rates Curve')
```

## Results


1. Example usage for animating the swap curve over time: 
   <div align="center">
       <img src="https://i.giphy.com/media/v1.Y2lkPTc5MGI3NjExNTBlbTF3ZTZ2MHlhajFocnRvNWNlZXYxN2M4ZGRuMXNweTZmZm9iZCZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/PjTjBN9MLcqCY7XjeR/giphy.gif" alt="Swap Curve Animation" />
   </div>

2. Another usage for animate two curves on the same animation over time:
   <div align="center">
       <img src="https://i.giphy.com/media/v1.Y2lkPTc5MGI3NjExZzhreHkzdWJnanZjZmQwMGJ3YW1kYmJ6bzV4ZHZ1cTF0amhwMGc0MiZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/eKZhf0rC7PlflH5yA5/giphy.gif" alt="Swap Curve Animation" />
   </div>

## Customization

- You can adjust the maturities or swap rates in the dataset by modifying the input `DataFrame` (`df`) and passing custom maturities.
- The animation speed can be adjusted by changing the `fps` in the `FuncAnimation` function.

## License

This project is licensed under the MIT License. See the `LICENSE` file for details.
