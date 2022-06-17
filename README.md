# Codebase for
* "A Multi-Trajectory Monte Carlo Sampler"
* "Robust Inference Based On the Complementary Hamiltonian Monte Carlo"
* orthogonal multi-trajectory sampler
* method4/Sampler4

# Theory

Hamiltonian Dynamics:

$\dot{q}=H_{p}=K_{p}$

$\dot{p}=-H_{q}=-U_{q}-K_{q}$

Euler integration:

$q_{1}= q_{0}+ \delta\times K_{p}(p_{0},q_{0})$

$p_{1}= p_{0} - \delta\times \left( U_{q}(q_1)+K_{q}(p_0,q_1) \right)$

The Kinetic energy with a parameter $r$:

$K_{r}(p,q)=K(p,q,r)=\frac{1}{2}q^{T}\cdot U_{qq}^{-r}\cdot q$

For non-positive (and positive) definite Hessian:

$U_{qq}^{-r}:=V\cdot(\left|\Lambda\right|^{-r} \odot \text{sign}(\Lambda)\cdot{}V^{T}$

The kinetic energy function that permits correct sampling of multivariate normal distributions utilizes the $K_{0.5}$ .

If the system comprises several particles with collision effects, it is intuitively apparent that there is no difference in jumping probability between the simulation trajectory's two terminals. Therefore, for a multi-particle system with interacting particles, if total energy conservation is enforced during collision, the mutual jump probability of any particle's position at either end of the trajectory is equal, and the following equation follows

$P(q_{0 } \to q_{1}) = P(q_{1 } \to q_{0})$

Thus, the Metropolis algorithm's acceptance probability can be utilized to determine if a particle jumps to the new position
$\alpha=e^{U(q_{1})-U(q_{0})}$

# Usage

1. load the sampler.

```
SetDirectory[NotebookDirectory[]];
<< "Sampler`";
```
2. Ignore the $K_q$ (optionally) to speed up computation. 

```
Kq[p_, q_, r_] = 0;
```

3. define the potential energy, i.e. the negative logarithm of probability density function.

```
rho = 1 - 1/10^15;
SIGMA = {{1, rho}, {rho, 1}};
U[x_, y_] = 1/2 Simplify[{x, y}.LinearSolve[SIGMA, {x, y}]];
```
4. Define the derivative functions. 

```
Uq[x_, y_] = D1[U[x, y], {x, y}];
Uqq[x_, y_] = D2[U[x, y], {x, y}];
Uqqq[x_, y_] = D3[U[x, y], {x, y}];

```

5. Run the sampler.
```
Dim = 2;
BURNIN=5000;
ITERATIONS=10000;
```

6. Check the result.

```
 QS1 = QS.MatrixPower[SIGMA, -.5];
StandardDeviation[QS1]
ListPlot[{QS, QS1}, PlotStyle -> Opacity[1], AspectRatio -> 1, 
 PlotLegends -> {Samples, Transformed}]
```

> {0.99964, 1.0068}

![scatter plots](bn2.png "scatter plots")
