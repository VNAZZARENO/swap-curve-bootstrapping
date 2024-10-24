# SOFR Swap Curve Bootstrapping

This repository contains a Python script that bootstraps a SOFR (Secured Overnight Financing Rate) swap curve, calculates zero rates from discount factors.

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

# Theoretical Background

## Motivation

The transition from LIBOR to the Secured Overnight Financing Rate (SOFR) marks a significant shift in global financial markets. SOFR, as a key interest rate benchmark, plays a crucial role in the valuation of interest rate derivatives, risk management, and financial instrument pricing. Modeling the SOFR curve is essential for market participants to price and hedge various interest rate products. This project provides a Python-based framework to bootstrap the SOFR swap curve, utilizing historical SOFR data from the Federal Reserve Economic Data (FRED) platform.

## Bootstrapping Methodology

We bootstrap the swap curve on 3 different maturity types : short-term, medium-term and long-term.
Each maturities uses a type of asset : deposits, FRAs and swaps.

### Short-Term Cash Deposits

For the short-term maturities, we start by initializing the discount factors for the overnight (O/N) and tomorrow/next (T/N) rates. The discount factor $D_{\text{O/N}}$ for overnight rates is calculated as:

$$
D_{\text{O/N}} = \frac{1}{1 + q_{\text{O/N}} \cdot \frac{d_{\text{O/N}}}{360}}, \quad i = 0
$$

where $q_{\text{O/N}}$ is the overnight rate and $d_{\text{O/N}}$ is the day count. From this, the zero rate $Z_{\text{O/N}}$ is derived as:

$$
Z_{\text{O/N}} = -100 \cdot \frac{\ln(D_{\text{O/N}})}{d_{\text{O/N}} / 365}
$$

Next, the discount factor $D_{\text{T/N}}$ for the tomorrow/next rate is calculated using:

$$
D_{\text{T/N}} = \frac{D_{\text{O/N}}}{1 + q_{\text{T/N}} \cdot \frac{d_{\text{T/N}}}{360}}, \quad i = 1
$$

where $q_{\text{T/N}}$ is the T/N rate. The zero rate $Z_{\text{T/N}}$ is:

$$
Z_{\text{T/N}} = -100 \cdot \frac{\ln(D_{\text{T/N}})}{d_{\text{T/N}} / 365}
$$

For subsequent maturities (e.g., 1-week, 1-month, etc.), the discount factor $D_i$ is calculated recursively using the previous discount factor:

$$
D_i = \frac{D_{\text{T/N}}}{1 + q_i \cdot \frac{d_i}{360}}, \quad i \geq 2
$$

The corresponding zero rate for these maturities is:

$$
Z_i = -100 \cdot \frac{\ln(D_i)}{d_i / 365}, \quad i \geq 2
$$



### Medium-Term FRAs

For the medium-term maturities (Forward Rate Agreements), we first initialize with the **stub rate**, which is calculated using interpolation of discount factors from the short-term maturities. The discount factor for the stub is given by:

$$
D_{\text{stub}(t)} = D(t_0) + \frac{D(T) - D(t_0)}{T - t_0} \cdot (t - t_0)
$$

where $t_0$ and $T$ are the short-term maturity points and $t$ is the stub maturity. Once the stub discount factor is calculated, the stub rate is derived as:

$$
z_{\text{stub}} = -100 \cdot \frac{\ln(D_{\text{stub}})}{d_{\text{stub}} / 365}
$$

Now that the stub rate has been established, the discount factor for the first FRA can be calculated as:

$$
D_1 = \frac{D_{\text{stub}}}{1 + q_1 \cdot \frac{d_{\text{FRA}}}{360}}, \quad i = 2
$$

where $q_1$ is the quoted rate for the first FRA and $d_{\text{FRA}}$ is the day count. The zero rate for the first FRA maturity is:

$$
Z_1 = -100 \cdot \frac{\ln(D_1)}{(t_1 - t_{\text{stub}}) / 365}
$$

For subsequent FRAs, the discount factor is calculated recursively using:

$$
D_i = \frac{D_{i-1}}{1 + q_i \cdot \frac{d_{\text{FRA}}}{360}}, \quad i \geq 3
$$

And the corresponding zero rate for these maturities is:

$$
Z_i = -100 \cdot \frac{\ln(D_i)}{(t_i - t_0) / 365}, \quad i \geq 3
$$


The curve construction for medium-term maturities begins with the appropriate stub rate and then uses a recursive relationship for the remaining FRAs.

### Long-Term Swaps

For longer maturities, we use swap rates to extend the curve. The par swap rate $r_{\text{par}}$ is calculated using the following formula:

$$
r_{\text{par}} = \frac{D_{\text{TIN}} - D_T}{\sum_{i=1}^{T} Y_i \cdot D_i}
$$

where $Y_i$ is the year fraction for each payment period, and $D_i$ represents the discount factor for each maturity.

To calculate the discount factor for longer maturities (from the swap rates), we use:

$$
D_T = \frac{1 - r_{\text{par}} \cdot \sum_{i=1}^{T-1} Y_i \cdot D_i}{1 + Y_T \cdot r_{\text{par}}}
$$

The zero rate for each maturity can then be derived as:

$$
Z_T = -100 \cdot \frac{\ln(D_T)}{d_T / 365}
$$

where $d_T$ is the number of days until maturity.

In cases where swap rates for certain maturities are not quoted, linear interpolation is used to approximate the rates. For unquoted years like 11Y, 13Y, 14Y, and so on, we use interpolation to find the zero rate and discount factor.


## Conclusion

This method provides a detailed and reliable swap curve based on SOFR data and other fixed-income assets (FRAs, SWAPs...). The bootstrapped zero-coupon curve allows for accurate pricing and valuation of financial instruments.

1. Clone this repository:

```bash
git clone https://github.com/yourusername/swap-curve-bootstrapping.git
cd swap-curve-bootstrapping
```

2. Install the required dependencies:

```bash
pip install -r requirements.txt
```

3. Run the Jupyter Notebook to perform the following tasks:
    - Compute the short and medium term discount factors and zero-rates
    - Compute the long-term discount factors and zero-rates
    - plot the whole SOFR swap curve

## License

This project is licensed under the MIT License. See the `LICENSE` file for details.
