---
tags:
aliases:
  - issue
---

this is an issue report, it's purpose is to note down changes in the upcoming releases of my work. Read about the point of departure and about the development path of the project in issue log. Read about the new releases and updates in the update log.


**date of issue**
14-08-2026
### Issue log

reconstruction of holograms creates objects out of recorded wavefields. reconstruction of captured holograms foremost depends on the holographic setup. 2 setups prove themselves, the 
- off-axis setup
- in line setup
these methods differ in how the reference wave is projected. 


#### The in-line setup
in in-line setup the object and reference wave are transmitted parallelly and so create diffraction solutions all in the same axis. 

![[Pasted image 20260814140627.png]]

because of the overlapping, filtering in the frequency domain isn't possible. this is the reason to why all previous reconstructions had seen the blinding DC light
![[Pasted image 20260814142347.png|423]]


today we are looking into in-line reconstruction of holograms and hope to make a comprehensive guide to quality and non-artefact reconstructions. all the how-to research for today
- produce object wavefield
- saving interference data 
- selecting the proper diffraction algorithms
- artefact removal methods
- numerical reconstruction methods

###### 1. producing the wavefield
producing the object wavefield is done by a point-cloud method PCM. Point wavefields are saved and summed into a single pattern.

*also read: PCM is a highly computationally expensive algorithm. see how to optimize the PCM calculations time with Look up tables LUT and Wavefront Recording Planes (WRP) [link](https://gemini.google.com/app/f1200e024c688995?hl=pl)*


this mathematically is formulated by the $O = A_Oe^{i\phi_O}$  , where $A_O$ is the amplitude equal one, and $e^{i\phi_O}$ is the phase co-efficient and plays the important part in the depth representation.

###### 2. saving the data
holographic memory had certain limits, due to the complications of saving a wavefield. in recorded digital holography, the digital sensor (ccd) was only capable of capturing image intensity. the intensity image hologram isn't enough information for in-line holography. for the actual object shape and depth is cued in the phase. to read this phase, mathematical algorithms are provided, called **phase retrieval algorithms**



in computer generated holography, complex wavefronts can be saved and stored easily in large matrices. phase and intensity can be easily reproduced by reading the complex magnitude (amplitude) or angle (phase). 


**3. numerical reconstruction methods**
numerical reconstruction algorithms simulate the diffraction of light on a SLM. the methods are :
- angular spectrum method 
- fresnel transformation method

aliasing and zero-order beam are the cause the main artefacts of the recosntructed image. to improve quality, authors recomend adding a zero-padding algorithm and a DC-filtering algorithm.


### Update log

in the last update we added a **second** **hologram phase extraction technique**. this is the phase shifting algorithm, it uses 4 phase shifts to create four raw intensity interferograms. four images are combined in a algorithm to calculate the expected wavefield and later extrude phase. 

```
    # 5. Simulate 4-step Phase-Shifting Interference

    # Define a simple on-axis plane reference wave (amplitude matched to object wave for max contrast)

    ref_amplitude = torch.max(torch.abs(hologram_field))

    reference_wave = torch.full_like(hologram_field, ref_amplitude, dtype=torch.complex64)

  

    shifts = [0.0, 3.14159265359 / 2, 3.14159265359, 3 * 3.14159265359 / 2]

    intensity_frames = []

  

    for delta in shifts:

        # Create the phase-shifted reference beam

        phase_shift = torch.complex(

            torch.tensor(np.cos(delta), dtype=torch.float32, device=device),

            torch.tensor(np.sin(delta), dtype=torch.float32, device=device)

        )

        shifted_reference = reference_wave * phase_shift

  

        # Superimpose object and reference waves

        interference = hologram_field + shifted_reference

  

        # Camera sensor records intensity (amplitude squared), losing the direct phase

        intensity = torch.abs(interference) ** 2

  

        # Move to CPU to free VRAM, ready for image saving

        intensity_frames.append(intensity.cpu().numpy())

  

    # Returns a list of 4 NumPy matrices: [I1, I2, I3, I4]

    return intensity_frames
```

the four matrices I1 I2 I3 I4 are four interferograms in different phase (0, pi/2, pi, 3pi/2). to calculate the phase an algorithm is presented

$$\phi_O(x,y) = \text{atan2}(I_2 - I_4, I_1 - I_3)$$


```
# 1. Reconstruct the raw complex field (ignoring the constant reference multiplier)

complex_field = (I1 - I3) + 1j * (I2 - I4)

  

# 2. Extract the phase map [-pi, pi]

# np.arctan2 automatically handles the quadrant signs correctly

phase_map = np.arctan2(I2 - I4, I1 - I3)

  

# 3. Extract the amplitude map

amplitude_map = np.sqrt((I1 - I3)**2 + (I2 - I4)**2) / 4.0

  

plt.imshow(phase_map, cmap='gray')

plt.title(f"Matryca 2D zawierająca wartości fazy hologramu")

plt.show()
```

![[Pasted image 20260815140334.png]]

the phase map is 
- phase shifting method
![[Pasted image 20260815140400.png]]

- point-cloud method (interceptor.png)
![[Pasted image 20260815140454.png|460]]

**zero padding**

a new applied zero-padding algorithm has been added to prevent from aliasing 

```
    pad_y, pad_x = Ny // 2, Nx // 2

    U_padded = np.pad(U_initial, ((pad_y, pad_y), (pad_x, pad_x)), mode='constant', constant_values=0)

    Ny_pad, Nx_pad = U_padded.shape
    
    ...
    
    
```

the new size of matrices is twice the size of the original (1000x1000 :: 2000x2000). this algorithm is made to reduce circular convolution of the edges and artefacts.

And additionally fixed the fresnel propagation algorithm. The new algorithm applies an outer chirp function. Secondly to prevent aliasing a physical proportion function has been applied to the reconstructed wavefield

```
    # Outer phase factor

    outer_chirp = np.exp(1j * (np.pi / (wavelength * z)) * (X_out**2 + Y_out**2))

    # Final field: multiplied by outer chirp and physical constants (dx*dy for discrete integral)

    U_final = U * outer_chirp / (1j * wavelength * z) * (pixel_size**2)
```

(reconstructed image - intercetor.png)
![[Pasted image 20260815203756.png]]

(the original interceptor)
![[Pasted image 20260815205244.png]]

