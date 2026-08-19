---
tags:
  - numerical-reconstruction
aliases:
  - issue
---
this is an issue report, it's purpose is to note down changes in the upcoming releases of my [[Graduation]] Thesis. Read about the point of departure and about the development path of the project in issue log. Read about the new releases and updates in the update log.



**date of issue**
19-08-2026
### Issue log

#### numerical reconstruction
in this issue we are going to work on numerical reconstruction. compare and select one dominant diffraction method [[Single Fast Fourier Transformation]] (**S-FFT**) or **ASM**. i want to add a angular spectrum method to test the diffraction method. 

On one hand **ASM** has smaller depth, would be convenient for focal stacking algorithms, with short focal range and small increments. 

On the other hand **S-FFT** has longer depth range, which would allow to fully use the perspective projection of a scene. This would allow for wide-angular Field of View **FOV**. 
#### depth visualize
I want to add a [[Focal Stacking]] algorithm to achieve better [[depth accomodation]]. 

#### 3D scene works
also i want to work on the 3D scene creating algorithm to check the arbitrary scene units and its proper transformation to metric units. 

I also want to work on a perspective projection with occlusion culling that would allow to incorporate a wide-angle Field of View. To do this i need to implement a perspective projection algorithm to create the z-buffer. 

![[Pasted image 20260819204953.png]]

### Update log

#### the suggested gemini code
```
from scipy.signal import fftconvolve

  

def scaled_fresnel_czt(u_in, wvl, d, dx_in, dy_in, dx_out, dy_out):

    """

    Simulates wavefield diffraction using a single Chirp Z-Transform (CZT) step.

    This allows arbitrary scaling of the output plane pixel pitch (dx_out, dy_out),

    unlike standard FFT-based Fresnel propagation which locks the output scale.

    Parameters:

        u_in   : 2D complex numpy array of the input wavefield

        wvl    : Wavelength of the light (meters)

        d      : Propagation distance (meters). Negative for reconstruction.

        dx_in, dy_in   : Pixel pitch of the input plane (meters)

        dx_out, dy_out : Pixel pitch of the output plane (meters)

    """

  
  

    u_in = np.exp(1j * phase)

  

    Ny, Nx = u_in.shape

    # Create integer index grids for the input plane

    ny, nx = np.meshgrid(np.arange(-Ny//2, Ny//2),

                         np.arange(-Nx//2, Nx//2), indexing='ij')

    # Physical coordinates

    x1, y1 = nx * dx_in, ny * dy_in

    x2, y2 = nx * dx_out, ny * dy_out

    # Wavenumber

    k = 2 * np.pi / wvl

    # Scaling parameters for the Chirp Z-Transform

    alpha_x = (dx_in * dx_out) / (wvl * d)

    alpha_y = (dy_in * dy_out) / (wvl * d)

    # ---------------------------------------------------------

    # STEP 1: Input Plane Phase Multiplication

    # ---------------------------------------------------------

    # Standard Fresnel quadratic phase

    P_in = np.exp(1j * (k / (2 * d)) * (x1**2 + y1**2))

    # CZT pre-multiplication chirp (A-term)

    A_term = np.exp(-1j * np.pi * (alpha_x * nx**2 + alpha_y * ny**2))

    # Prepare the input matrix for convolution

    A = u_in * P_in * A_term

    # ---------------------------------------------------------

    # STEP 2: The Chirp Z-Transform (via FFT Convolution)

    # ---------------------------------------------------------

    # Generate the convolution kernel (B-term) for Bluestein's method.

    # It must be sized (2N-1) to evaluate the linear convolution fully.

    ny_B, nx_B = np.meshgrid(np.arange(-Ny+1, Ny),

                             np.arange(-Nx+1, Nx), indexing='ij')

    B = np.exp(1j * np.pi * (alpha_x * nx_B**2 + alpha_y * ny_B**2))

    # Perform the single Fast Fourier Transform step (convolution theorem)

    # fftconvolve automatically handles zero-padding and executes the FFT -> multiply -> IFFT

    S = fftconvolve(A, B, mode='valid')

    # ---------------------------------------------------------

    # STEP 3: Output Plane Phase Multiplication

    # ---------------------------------------------------------

    # CZT post-multiplication chirp (C-term)

    C_term = np.exp(-1j * np.pi * (alpha_x * nx**2 + alpha_y * ny**2))

    # Standard Fresnel quadratic phase for the output plane

    P_out = np.exp(1j * (k / (2 * d)) * (x2**2 + y2**2))

    # Final scaling and phase aggregation

    coeff = np.exp(1j * k * d) / (1j * wvl * d)

    # Multiply by the input area differential (dx_in * dy_in) for the integral

    u_out = coeff * S * C_term * P_out * (dx_in * dy_in)

    return u_out
```

##### How this works mathematically: (chat gpt)

1. **Diffraction Integral:** To propagate the wave, we evaluate the Fresnel diffraction integral. A normal FFT rigidly forces the output pixel size to strictly equal $\frac{\lambda d}{N \Delta x}$, which creates immense scaling problems for short distances.
    
2. **The CZT / Bluestein Trick:** We expand the quadratic phase term $(x_1 - x_2)^2$ from the integral into $x_1^2 - 2x_1x_2 + x_2^2$.
    
3. **The Single FFT:** The cross term $-2x_1x_2$ triggers a linear convolution. The function maps the grids, calculates the pre-chirp (`A`), and convolves it with the chirp kernel (`B`) using `scipy.signal.fftconvolve`. This handles the exact sequence of the FFT $\rightarrow$ Element-wise Multiplication $\rightarrow$ Inverse FFT all in one computationally optimized pass.

read more on [[Single Fast Fourier Transformation]]

