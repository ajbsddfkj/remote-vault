this is an issue report, it's purpose is to note down changes in the upcoming releases of my work. Read about the point of departure and about the development path of the project in issue log. Read about the new releases and updates in the update log.


**date of issue**
15-08-2026
### Issue log

in this issue we cover the new phase retrieval algorithm to fix the reconstruction of the hologram. 
### Update log

the new GS-style algorithm is a supporting block of code that does phase retrieving on a phase matrix of the point-cloud wavefield. 

so the code actually compares the propagation of the light from hologram plane to image plane. it does so with fourier domain transforms. the image constraint is the z_buffer mask *target_amplitude* and the phase is the *phase_result* of the PCM. 

the new numerical reconstruction quality is astonishing. intensity images are a reflection of the rendered images. 

##### the code
```
import numpy as np

import matplotlib.pyplot as plt

import torch

  

def propagate_forward(field):

    """Propagates the wavefield from the Hologram (SLM) plane to the Image plane."""

    return np.fft.fftshift(np.fft.fft2(np.fft.ifftshift(field)))

  

def propagate_backward(field):

    """Propagates the wavefield from the Image plane back to the Hologram plane."""

    return np.fft.fftshift(np.fft.ifft2(np.fft.ifftshift(field)))

  

def optimized_hybrid_gs(rendered_target_amplitude, initial_pcm_phase, iterations=30):

    # Start with the phase calculated from the Point Cloud Method

    slm_phase = initial_pcm_phase.copy()

    print(f"Starting optimized GS for {iterations} iterations...")

    for i in range(iterations):

        # Step A: Hologram Plane - Apply Phase-only SLM constraint

        slm_field = 1.0 * np.exp(1j * slm_phase)

        # Step B: Propagate to Image Plane

        image_field = propagate_forward(slm_field)

        # Step C: Image Plane - Apply PERFECT Rendered Target Constraint

        image_phase = np.angle(image_field)

        constrained_image_field = rendered_target_amplitude * np.exp(1j * image_phase)

        # Step D: Propagate back to Hologram Plane

        slm_field_back = propagate_backward(constrained_image_field)

        # Update the SLM phase for the next loop

        slm_phase = np.angle(slm_field_back)

    print("Optimization complete.")

    return slm_phase

  

# =====================================================================

# INTEGRATION WITH JUPYTER NOTEBOOK VARIABLES

# =====================================================================

  

# 1. CREATE TARGET AMPLITUDE FROM YOUR Z-BUFFER

# Your z_buffer has float('inf') for the background and numeric depth values for the object.

# We map the object to 1.0 (light) and the infinity background to 0.0 (dark).

target_amplitude = np.zeros_like(z_buffer, dtype=np.float32)

target_amplitude[z_buffer != float('inf')] = 1.0

  

# 2. EXTRACT PHASE SEED FROM PCM RESULT

# phase_result was calculated on the GPU, so we must safely bring it back to CPU & Numpy

if torch.is_tensor(phase_result):

    pcm_phase_seed = phase_result.cpu().numpy()

else:

    pcm_phase_seed = phase_result

  

# 3. RUN THE IMPROVED HYBRID ALGORITHM

# We pass the clean, binary 2D mask AND the depth-accurate phase seed

optimized_phase = optimized_hybrid_gs(target_amplitude, pcm_phase_seed, iterations=30)

  

# 4. SIMULATE THE OPTICAL RECONSTRUCTION

final_slm_field = 1.0 * np.exp(1j * optimized_phase)

simulated_reconstruction = np.abs(propagate_forward(final_slm_field))

  

# 5. PLOT THE RESULTS

fig, axes = plt.subplots(1, 3, figsize=(15, 5))

  

axes[0].imshow(target_amplitude, cmap='gray')

axes[0].set_title("Target Amplitude Constraint\n(Derived directly from z_buffer)")

axes[0].axis('off')

  

axes[1].imshow(optimized_phase, cmap='twilight')

axes[1].set_title("Optimized Phase-Only Hologram\n(To display on SLM)")

axes[1].axis('off')

  

axes[2].imshow(simulated_reconstruction, cmap='gray')

axes[2].set_title("Simulated Optical Reconstruction\n(Phase-only result)")

axes[2].axis('off')

  

plt.tight_layout()

plt.show()
```

