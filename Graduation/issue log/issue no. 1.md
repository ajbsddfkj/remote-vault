this is an issue report, it's purpose is to note down changes in the upcoming releases of my work. Read about the point of departure and about the development path of the project in issue log. Read about the new releases and updates in the update log.


**date of issue**
08-08-2026
### Issue log

The current state of the art workspace uses a GPU CUDA hardware to accelerate calculation of the complex wavefront matrix. 
The GPU in use is a T4 Google GPU with 16 GB VRAM. This allows for streaming a batch of approx. 60 points for wavefront calculations. 

The calculation time is measured, and the process is displayed by a progress bar. Approximate time for calculating a full size hologram is 7:56 minutes (the metrics were acquired for a 2048 x 2048 size matrix, 164301 point cloud).

The hologram is saved on a google drive and can be uploaded once more. 

In the current state the hologram isn't able for reconstruction. the reconstruction method in use is an angular spectrum method (ASM). the method has a certain error, and after running code the image doesn't appear. 

the effect that causes this is unknown, the best solutions to this are:
- selecting a different reconstruction method
- creating a band-limited angular spectrum method 
- creating an autofocusing code.

the ASM is very sensitive to the z distance, and has a cap off work distance. 

$$
z < \frac{N\Delta x^2}{\lambda}
$$
for the physical setup in the laboratory the cut off distance is approx. 45 mm, while the projection distance is above 1 meter. The solution to this is to use a different reconstruction method, e.g. the **Fresnel Diffraction Method**. 



### Update log

the update focuses on fixing the reconstruction method. first off the ASM was replaced by the FFT method. This is due to the physical limitations off the reconstruction distance of the ASM. 

```
from google.colab import drive

drive.mount('/content/drive')

  

import numpy as np

import matplotlib.pyplot as plt

from PIL import Image

  

def fresnel_reconstruct(hologram_path, z, wavelength, pixel_size):

    """

    Reconstructs a digital hologram using the Fresnel diffraction (FFT) method.

  

    Parameters:

    hologram_path (str): Path to the recorded hologram image file.

    z (float): Reconstruction distance in meters.

    wavelength (float): Wavelength of the laser used in meters.

    pixel_size (float): Pixel pitch of the camera sensor in meters.

    """

  

    # 1. Load the hologram and convert it to a 2D float array (grayscale)

    img = Image.open(hologram_path).convert('L')

    hologram = np.asarray(img, dtype=np.float64)

  

    # Get image dimensions (Ny = rows, Nx = columns)

    Ny, Nx = hologram.shape

  

    # 2. Create the spatial coordinate grid

    # We center the grid at (0,0) to properly apply the spherical chirp function

    x = np.arange(-Nx/2, Nx/2) * pixel_size

    y = np.arange(-Ny/2, Ny/2) * pixel_size

    X, Y = np.meshgrid(x, y)

  

    # 3. Calculate the Chirp Function (Quadratic Phase Factor)

    # Equation: exp( i * pi / (lambda * z) * (X^2 + Y^2) )

    chirp = np.exp(1j * np.pi / (wavelength * z) * (X**2 + Y**2))

  

    # 4. Multiply the hologram by the chirp function

    # Note: We assume the reference wave R(X,Y) is a simple uniform plane wave (R=1).

    # If your setup used a spherical reference wave or an off-axis angle,

    # you would multiply by that specific R(X,Y) field here as well.

  

    phase = (hologram / 255.0) * (2 * np.pi)

  

    U_initial = np.exp(1j * phase)

  

    complex_field = U_initial * chirp

  

    # 5. Perform the 2D Fast Fourier Transform

    # - ifftshift: aligns the center of our grid to the edges (expected by the FFT algorithm)

    # - fft2: computes the actual 2D Fourier Transform

    # - fftshift: moves the zero-frequency (DC) component back to the center of the image

    U = np.fft.fftshift(np.fft.fft2(np.fft.ifftshift(complex_field)))

  

    # 6. Extract the Reconstructed Intensity and Phase

    intensity = np.abs(U)**2

    phase = np.angle(U) # Mathematically equivalent to arctan(Im / Re)

  

    # Optional: Log-scale the intensity for better visibility if the DC term is very bright

    intensity_log = np.log10(intensity + 1)

  

    return intensity_log, phase

  

# ==========================================

# Example Usage

# ==========================================

if __name__ == "__main__":

    # Define your physical setup parameters

    IMAGE_PATH = "/content/drive/MyDrive/pointcloud/CGHs/zmienNazwe.png" # Replace with your hologram image file

  

    # Example values (e.g., a He-Ne laser and a typical CMOS camera)

    WAVELENGTH = 632.8e-9       # 632.8 nm in meters

    PIXEL_SIZE = 3.74e-6         # 5.0 micrometers in meters

    DISTANCE = 45             # 115 cm reconstruction distance in meters

  

    try:

        # Run the reconstruction

        recon_intensity, recon_phase = fresnel_reconstruct(

            IMAGE_PATH, DISTANCE, WAVELENGTH, PIXEL_SIZE

        )

  

        # Plot the results

        fig, axes = plt.subplots(1, 2, figsize=(12, 6))

  

        # Display Intensity

        ax1 = axes[0]

        im1 = ax1.imshow(recon_intensity, cmap='gray')

        ax1.set_title(f"Reconstructed Intensity (z={DISTANCE}m)")

        fig.colorbar(im1, ax=ax1, fraction=0.046, pad=0.04)

  

        # Display Phase

        ax2 = axes[1]

        im2 = ax2.imshow(recon_phase, cmap='gray')

        ax2.set_title("Reconstructed Phase")

        fig.colorbar(im2, ax=ax2, fraction=0.046, pad=0.04)

  

        plt.tight_layout()

        plt.show()

  

    except FileNotFoundError:

        print(f"Error: Could not find the image '{IMAGE_PATH}'. Please provide a valid hologram image path.")
```

during reconstruction the scalar image uploaded from the drive is rescaled into complex.float64 type.

```
 hologram = np.asarray(img, dtype=np.float64)
 
 phase = (hologram / 255.0) * (2 * np.pi) 

 U_initial = np.exp(1j * phase)

 complex_field = U_initial * chirp
 
 
```

hologram calculation method fixed, the program deletes only the idle points (float(inf)). 

```
valid_mask = ~torch.isinf(flat_depth)
```

z units in the object have been updated - from the arbitrary 3d units to the pixel units (note: this is still not a metric unit).

```
def project_and_scale(v):

    # Centrujemy obiekt i skalujemy go do wymiarów ekranu [0, width-1] i [0, height-1]

    x_pixel = int(((v[0] - x_center) / max_range + 0.5) * (height - 1))

    y_pixel = int(((v[1] - y_center) / max_range + 0.5) * (height - 1))

    z_depth = ((v[2] - z_center) / max_range) * (width - 1)

    return np.array([x_pixel, y_pixel, z_depth])
    
 
```

