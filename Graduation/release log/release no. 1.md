this is an release note, it's purpose is to document the state-of-the-art of my [[Graduation]] Thesis.  Here you will find the general features, incorporated hardware and software technology and also the ideas of improvements. 

**name of release**
sandbox_zapis16082026

**date of release**
16-08-2026
### The general features of code

The code creates a **computer generated hologram CGH** out of 3D mesh objects. This method is derived from a **occlusion-culling point-cloud method**. The generation and later reconstruction is presented in the later steps:
##### Rendering the 3D mesh
Here points of the point-cloud are projected orthographically on to the SLM grid size kernel called the **z-buffer**. This simple occlusion method creates a point-cloud of the face of the mesh for realistic reconstruction. I was inspired by a publication in optimizing computer generated holography ([the full article](https://www.mdpi.com/1424-8220/25/20/6492)).


![[Pasted image 20260816200657.png]]
*image 1 - orthographic projection (presented on image 'a'), the rendering algorithm for occlusion culling*
##### Generating the hologram
The code features a **computer generated hologram (CGH)** algorithm. This algorithm creates an in-line Gabor hologram. I was inspired by the algorithm proposed in the previous article, is a **point cloud method hologram**. Here each point form the point-cloud creates a spherical wavefield called a **sub-hologram**. To record the wavefield we measure the Euclidean distance of the point from each pixel of the hologram plane. 
![[Pasted image 20260816202216.png]]
*image 2 - the fresnel schere - the wavefield of a single point*

This method is repeated for each point of the point cloud, in a $H \cdot W \cdot N$ size grid. However this matrix size is a **computational burden**, where memory required is over **4 TB** and it would definitely **overfill the VRAM**. This introduces the division into batches of points, which increases the speed of the parallel calculations, while putting a safety cap on the memory usage.

This hologram is a representation of the 3D scene and hints the depth cues in this hologram.

##### Phase retrieval optimization
Since the proposed algorithm hints depth reconstruction at a fixed depth would only give one focus plane. Here I applied a Gerchberg-Saxon phase retrieval algorithm to reconstruct images in the focus distance. This is algorithm creates a 2D phase map that compensates the loss of the amplitude in the point-cloud method. 

##### Numerical Reconstruction
The diffraction simulation method is a approximation of the Rayleigh-Sommerfeld diffraction integral and is done by fast-fourier transforms. The propagation distance is greater then the maximal Angular Spectrum Method distance $z_{max}\leq \frac{N\Delta x^2}{\lambda}$. Hence I propose a **single Fast Fourier Transform (S-FFT)** algorithm, using a chirp function to correct the field. read the [chat](https://gemini.google.com/app/fa9bfd5a563b2e7f?hl=pl)


![[Pasted image 20260816212104.png]]
### Incorporated hardware

##### GPU Acceleration
The calculation algorithm uses a virtual GPU for acceleration and a CUDA framework for parallel computing to bring the CGH generation closer to real-time performance. The program uses a NVIDIA A100-SXM4-40GB with 40 GB high bandwidth memory. 

The hologram wavefield is calculated in a PyTorch framework tensor matrix, running on a NVIDIA GPU, enabling for high speed parallel computation. 

*Computation time per scene:*
**Jinx**: 6:23 min (size 389 K)
**Interceptor**: 4:35 min (est)
**Human**: 9:35 min (size 550 K)

##### Target VRAM size
The hologram is a 4K (3840 x 2180 pixels) matrix, consisting of float64 precision units. This in a batch of 80 can allocate up to 5 GB of RAM per batch, and effective VRAM usage goes up to 30 GB.

##### Phase-output SLM
The optical reconstruction device uses a Holoeye Gaea 2.0 4K Spatial Light Modulator. This device is a phase-only modulator and so input value is to be a phase map. The introduced code saves images to a grayscale PNG file with 8-bit accuracy (0-255). 

### Ideas for improvement

the next release will improve the time performance of the generation algorithm and create a better numerical reconstruction free of the previous error.

##### the next key reinforcements:
- **support region calculations** - fixing the region of interest of the hologram for faster CGH calculations
- **precomputed look-up-table LUT** - a standard solution for accelerating CGH calculations
- **point interpolation**
- **nonuniform sampling** - reducing the number of samples that need to be calculated [read more](https://opg.optica.org/ao/fulltext.cfm?uri=ao-48-36-6841)
- **S-FFT algorithm with [[CZT chirp Z correction]] function** #numerical-reconstruction - a fine single fourier tranform algorithm that prevents aliasing [read more](https://gemini.google.com/app/fa9bfd5a563b2e7f?hl=pl)

