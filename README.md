# ASIC for American Option Pricing Using Monte‑Carlo Method

## Introduction
The option is a certificate for future buying or selling of a certain merchandise. American option is a type of option that allows holders to exercise the option rights any time not later than the expiration date. Hence, every possible stock price fluctuation from now to the expiration date needs to be considered when conducting option pricing.
This design implements the valuation of American call options using the Monte Carlo method. In terms of the random process equation of stock price fluctuations, several random option pricing paths are generated. By calculating the expected cashflows of all paths through regression, call option pricing is performed.
The chip is fabricated using 180nm UMC technology. Its summary is provided as follows.

![image](https://github.com/hsiang20/ICDLAB-American-Option-Pricing/assets/38748578/fbcac39b-e8c0-42e6-b653-167df2c9b6e3)
![image](https://github.com/hsiang20/ICDLAB-American-Option-Pricing/assets/38748578/f82e71da-544a-461b-ad02-e26c27797582)

## Implementation Details
### Overview
We begin by utilizing the Sobol random number generator to produce a 16-bit random variable. This variable is then input into the Inverse Cumulative Distribution Function (ICDF) to generate a Gaussian distributed signal 𝜀. By incorporating w, q, S0, and the generated 𝜀 into the daily stock price generator (Path Generator), we can generate daily stock prices (Paths) with randomness. Here, w and q are constants related to the stock price and volatility, and S0 is the current stock price. Subsequently, the daily stock prices and the strike price (K) of the American-style option are input into the Monte Carlo Core of the second part, resulting in a Cash Flow Matrix. Averaging the columns of this Cash Flow Matrix yields the pricing for the American-style option.
The overall architecture of this chip design is illustrated in Figure 1 and can be divided into two main parts. The first part generates daily random stock prices (Sobol RNG, ICDF, Path Generator), while the second part focuses on American-style option pricing (Pricing). The subsequent sections will provide a sequential explanation of these components.

| ![image](https://github.com/hsiang20/ICDLAB-American-Option-Pricing/assets/38748578/b8193afd-c2ab-45c5-8448-2b3ec51b80e5) |
|----------|
| Figure 1: Chip Overall Architecture |

### Principle and Architecture of Generating Random Stock Prices
During the generation of each price path, it is necessary to employ a random variable ε to simulate the stochastic nature of price fluctuations. Figure 2 depicts the generation process of ε. In this experiment, the Sobol low-discrepancy sequence is utilized as the foundation for generating random variables. These variables are then transformed into Gaussian random variables using the inverse of the cumulative distribution function of the Gaussian distribution, referred to as the Inverse Cumulative Distribution Function (ICDF). This conversion serves as a crucial step in generating the required price paths.

| ![image](https://github.com/hsiang20/ICDLAB-American-Option-Pricing/assets/38748578/2c268076-bc61-4b4f-9086-2d7e32b03cfe) |
|----------|
| Figure 2: Process of Generating Random Variable ε |

The Sobol low-discrepancy sequence is a form of quasi-random numbers. This approach provides a systematic way to generate the required sequence. While strictly speaking, the generated numbers are not entirely random, they offer a higher generation speed compared to true random numbers. Moreover, it has been demonstrated that Sobol sequences exhibit results similar to true random numbers in Monte Carlo simulations. This characteristic has led to their widespread adoption in financial and quantitative analysis.
The choice to use quasi-random numbers, such as Sobol sequences, rather than typical pseudo-random numbers, is aimed at addressing the issue of uneven distribution in multi-dimensional spaces. This phenomenon is demonstrated in Figure 3, where pseudo-random numbers are generated using the Mersenne Twister random number generator. Figure 4 illustrates the partitioning of the distribution range into small 20x20 grids. The discrepancy between the actual occurrence rate at the midpoint of each grid and the expected uniform distribution rate is calculated. The resulting visualization indicates a noticeable clustering effect on the right-hand side, highlighting the improvement brought about by using quasi-random numbers.

| ![image](https://github.com/hsiang20/ICDLAB-American-Option-Pricing/assets/38748578/40e90739-cf7c-4c49-90f4-c317dd6d2489) | ![image](https://github.com/hsiang20/ICDLAB-American-Option-Pricing/assets/38748578/ad0c571d-b227-49d0-b0ee-94c24b9a29da) |
|----------|---------|
| Figure 3: Distribution of 10,000 Low-Discrepancy Sequences and Pseudo-Random Number Points | Figure 4: Discrepancy Between the Occurrence Rate of Midpoints in Segmented Grids (Divided into 400 Grids) |

