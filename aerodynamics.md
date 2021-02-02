
https://www.aviationsystemsdivision.arc.nasa.gov/publications/hitl/rtsim/Toms.pdf

https://www.mathworks.com/help/aeroblks/index.html?s_tid=CRUX_lftnav

https://www.mathworks.com/help/aeroblks/aerodynamicforcesandmoments.html

https://www.mathworks.com/help/aeroblks/lightweight-airplane-design.html

https://www.intechopen.com/books/flight-physics-models-techniques-and-technologies/goal-and-object-oriented-models-of-the-aerodynamic-coefficients

https://www.grc.nasa.gov/WWW/k-12/VirtualAero/BottleRocket/airplane/kitetrim.html

https://www.youtube.com/watch?v=GKMBoGwLG1g

https://en.wikipedia.org/wiki/Flight_dynamics_(fixed-wing_aircraft)

# Drag Equation

$$
D = C_d \cdot \frac {\rho \cdot V^2} {2} \cdot A
$$

$C_d$ is coefficient of drag and contains all the complex dependencies and is usually determined experimentally. $\rho$ is the density of air, $V$ is velocity of air. $A$ is the reference area used when experimentally determining the $C_d$ (during wind tunnel testing). The area can be area of the wing, or cross section area of body, whatever you want. The idea is that you determine $C_d$ during miniture-model wind-tunnel testing. When you scale up to real-life size, $A$ will then become the real-life size, but $C_d$ should be the same value (in theory) given the same flight conditions (angle of attack, speed, density of air, etc). This is how $D$ can be estimated on real-life size airplane before it is built. 

https://www.grc.nasa.gov/www/k-12/airplane/drageq.html

# Lift Equation
$$
L = C_l \cdot \frac {\rho \cdot V^2} {2} \cdot A
$$

$A$ is wing area

https://www.grc.nasa.gov/www/k-12/airplane/lifteq.html

$ \frac {\rho \cdot V^2} {2} $ is often called dynamic pressure.

$\alpha$ = angle of attack
$\beta$ = side slip angle. Essentially the directional angle of attack.