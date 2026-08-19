---
tags:
  - holography
---
Holography unlike traditional 3D displays, create depth. VR causes strain due to the eyes staying focused (accommodated) on a 2 dimensional screen. Here holograms create real wavefronts that a real 3D object would scatter.

The process of generating three dimensional holograms is calculated precisely:



### 3D scene representation

#spatial-coordinates
Before calculating the wavefield the computer needs a mathematical model. The scene must be mapped in a way that preservers true spatial coordinates and not arbitrary units.

My algorithm uses a [[point cloud method]] that generates the scene. Once this scene is modelled the wavefront is calculated. 

### Wavefront representation

#numerical-reconstruction
Using mathematical models of wave diffraction like [[Single Fast Fourier Transformation]] the complex light wave at surface is calculated. The Fourier Transform is corrected using a [[CZT chirp Z correction]] technique.

Once this software is applied it serves as an engine for visualizing focal plane. Running reconstruction algorithms in loops we can simulate depth in stacks, like a 3D volume made of 2D slices. The applied technique is [[Focal Stacking]].

By changing the distance variable ($z$) in small increments it generates a stack of 2D images.

Another technique called [[Shape From Focus]] (SFF), build a depth map or a 3D point cloud from a focal stack. [Read more here](https://gemini.google.com/app/2ddee8245779f568?hl=pl)

![[Pasted image 20260818220740.png]]


### Phase encoding 

##### References

[[CZT chirp Z correction]]

[[Single Fast Fourier Transformation]]

[[point cloud method]]

[[issue no. 6]]
