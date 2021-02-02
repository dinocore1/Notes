

http://ceres-solver.org/

# Non-linear Least Squares
$$
\underset{x}{\text{min}} \frac {1} {2} \sum_{n=i}   {\mid\mid f_i(x_{i_1}, ... ,x_{i_k}) \mid\mid}^{2}
$$
$f_i(\cdot)$ is a Cost-Function that depends on the parameters $[x_{i_1}, ... ,x_{i_k}]$

## Rules Engine Problem

We can formulate the rules engine problem into a non-linear least squares problem. The parameters block ($[x_{i_1}, ... ,x_{i_k}]$) can be used to represent the kWh value setting for the charger for each time interval during charging. We can write several CostFunctions to compute a residual. For example, the Cost Function related to Daily Minimum Range could be $ f(x_i,...) = P_{range} - \sum_{i} x_i \cdot t $

### Minimize Carbon Impact

Carbon Intensity forecast can be obtained via Carbonara API.

$ R $ = range (miles)

$ E $ = battery energy ( $kWh$) 

$ P_{range} $ = desired vehical range

$ C $ = charge rate ($kW$)

$ M_{range} $ = coefficient to convert units of energy into miles

$ sigmoide(x) =  \frac {1} {1 + e^{-x}} = \frac {1} {2} + \frac {1} {2} \tanh ( \frac {x} {2} ) $ = logistic function

$ softplus(x) = ln(1 + e^x) $

## Functions

$ E = C * t $

## Daily Minimum Range Rule

The minimum number of miles that should be charged every day.

$ \Delta R = P_{range} - R $

$ E = softplus(\Delta R \cdot M_{range} - sum) $

## Charge by time

The time that charging should be complete every day.

## Charge with excess Solar

Dynamically adjust charge rate to maximize use of excess solar

$ S $ = available solar 

$$
residual = C - S
$$

## Charge using minimum carbon

$P_{dirty} = C - S$ = power from dirty carbon

$$
residual = sig ( P_{dirty} ) P_{dirty} I_{carbon} 
$$

$$
C = \frac {\frac {P_{range} - R } {M_{range}}} {time}
$$

$$



$$

$ \frac {\partial C} {\partial t} = \Delta E $