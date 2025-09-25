# Ion-Implantation-Profiling
Python script that generates an implantation profile from multiple implants using simple Gaussian. 


<img width="800" height="600" alt="image" src="https://github.com/user-attachments/assets/0a40f8e6-a743-4460-bc9d-40a00a5f3d1c" />

### Implant Profile Equations

We model the dopant concentration profile from multiple ion implants as the **sum of Gaussians**:

$$
C(z) = \sum_{i=1}^{N} \frac{D_i}{\sqrt{2\pi}\,\sigma_i}
\exp\!\left(-\frac{(z - R_{p,i})^2}{2\sigma_i^2}\right)
$$

Where:  
- $z$ = depth (cm)  
- $D_i$ = dose of implant $i$ (atoms/cm²)  
- $R_{p,i}$ = projected range for implant $i$ (cm)  
- $\sigma_i$ = straggle for implant $i$ (cm)
- $N$ = 4, to model a profile after four successive implants  



### Projected Range and Straggle

For arsenic in silicon:

$$
R_{p,i} = \frac{1025 \,\text{Å}}{200 \,\text{keV}} \cdot E_i
$$

$$
\sigma_i = \frac{215 \,\text{Å}}{200 \,\text{keV}} \cdot E_i
$$

with $E_i$ = implant energy in keV.  
(Units: Å; convert to cm by multiplying by $10^{-8}$.)


### Target Profile Constraints

These were given in the problem statement. Essentially, with four implants, we're trying to obtain a profile that is as flat as possible in the flat concentration region.


- Flat concentration:

$$
C(z) \approx 1 \times 10^{17} \ \text{cm}^{-3}, \quad 250 \,\text{Å} \leq z \leq 1000 \,\text{Å}
$$

- Shallow limit:

$$
C(z) < 1 \times 10^{15} \ \text{cm}^{-3}, \quad z < 100 \,\text{Å}
$$

- Deep limit:

$$
C(z) < 1 \times 10^{15} \ \text{cm}^{-3}, \quad z > 1700 \,\text{Å}
$$



### Least-Squares Formulation

We can set up a matrix to define the four Gaussians. Choose implant energies $E_i$, compute corresponding $R_{p,i}$ and $\sigma_i$, then solve for the doses $D_i$ that minimize:

$$
\min_{D_1,\dots,D_N} 
\sum_{z \in \text{grid}} \Big( C(z) - C_\text{target}(z) \Big)^2
$$

where $C(z)$ is the sum of Gaussians (above) and $C_\text{target}(z)$ is the desired profile. We get to "arbitrarily" choose the four energies of the implants. This lets us shape the profile.

---

### Effect of subsequent annealing on profile

The second script examines the effect of annealing on our generated doping profile. We do this by first assuming the surface to be at infinity relative to the peak and approximating the new straggle to be: 


<img width="188" height="47" alt="image" src="https://github.com/user-attachments/assets/c9a83976-8e4e-4648-a72b-b6ece23d447e" />


We can then see how the profile changes in response to (a) a 1000 degree C, 45 minute anneal, (b) after a RTP at 1100 degrees C for 10 seconds, which looks like: 

<img width="1000" height="600" alt="image" src="https://github.com/user-attachments/assets/a6f3ae7e-dc0f-40ca-82e0-59582e102760" />

So, given this plot, can we say the surface at infinity assumption is valid for both of these annealing cases? For part (a), no. The diffusion length is very large, which can be seen from just how broad the Gaussian is after the anneal. So, saying that surfaces don't affect the diffusion profile is just not valid. For part (b), however, RTP only causes a very moderate broadening of the profile, and you could say that, for the RTP case, the assumption is more valid. 





