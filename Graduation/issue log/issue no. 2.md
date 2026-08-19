---
tags:
aliases:
  - issue
---

this is an issue report, it's purpose is to note down changes in the upcoming releases of my work. Read about the point of departure and about the development path of the project in issue log. Read about the new releases and updates in the update log.



**date of issue**
11-08-2026
### Issue log

#spatial-coordinates 
This issue aims at fixing the reconstruction. First goal is to scale the z-depth of the rendered image to metric units. In this setup the next hologram will be generated and later tested for reconstruction. 

One correction is to add an offset value to the depth cue

to gain certainty that acquisition is correct, a must-have check list is mandatory:
- isotropic mesh scaling 
- added z-value offset to depth map and wave propagation function in reconstruction 
- 



### Update log

the issue changes now are to rescale the z-depth units of the 3D-mesh

```
x_range = x_max - x_min

y_range = y_max - y_min

z_range = z_max - z_min

...

def project_and_scale(v):

    # Centrujemy obiekt i skalujemy go do wymiarów ekranu [0, width-1] i [0, height-1]

    x_pixel = int(((v[0] - x_center) / max_range + 0.5) * (height - 1))

    y_pixel = int(((v[1] - y_center) / max_range + 0.5) * (height - 1))

    z_depth = ((v[2]) / z_range) * (width - 1)

    return np.array([x_pixel, y_pixel, z_depth])

```

for the purpose of reconstruction, the **program must retrieve** the wave propagation distance to recreate the object wavefield.

the corrupt z-distance makes things difficult, for reconstruction to reoccur. 

hence changes must be added to the previous corrupted update

```
z_range = z_max - z_min

max_range = max(x_range, y_range, z_range) * 1.2 # 20% marginesu

  

# Środek geometryczny modelu

x_center = (x_min + x_max) / 2

y_center = (y_min + y_max) / 2

z_center = (z_min + z_max) / 2 # Dodano środek dla osi Z

# dodano Globalny współczynnik skali (chroni proporcje nawet jeśli width != height)

scale = min(width, height) / max_range
...
    # Z również zostaje wyśrodkowane i przeskalowane jednorodnie

    z_depth = (v[2] - z_center) * scale

    return np.array([x_pixel, y_pixel, z_depth])

```


```
# dodano przesunięcie wzdłuż osi z o 100 mm - (czyli 0.1/ 3.74e-6 pikseli)

offsetZ = 0.1 / 3.74e-6

z_buffer[z_buffer != float('inf')] = z_buffer[z_buffer != float('inf')] + offsetZ
```


the wavefields will be calculated for the wrong depth parameters and reconstructed wavefields will bring out the shapeless object. 

![[Pasted image 20260811223630.png]]

![[Pasted image 20260811223648.png|477]]

![[Pasted image 20260811223602.png]]

this reconstruction has been done on the Jedi interceptor. the many reflected ghost images of the wings suggest anomaly in the reconstruction. this could be due to fftshift mistakes. 

![[Pasted image 20260811225306.png]]

![[Pasted image 20260811225317.png|436]]

![[Pasted image 20260811225328.png]]

the reconstruction on jinx doesn't suggest much. 