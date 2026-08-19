---
tags:
aliases:
  - issue
---

this is an issue report, it's purpose is to note down changes in the upcoming releases of my work. Read about the point of departure and about the development path of the project in issue log. Read about the new releases and updates in the update log.


**date of issue**
16-08-2026
### Issue log

the core aim of the issue is to add a script that saves the program parameters into a json file. the read and save functions will be added in each mathematical block of code
### Update log

dodano na początek programu kod zapisujący do pliku .json parametry globalne. pliki są dostępne w programie i w pliku .json pod tą samą nazwą.

w pliku zapisane są parametry:
- **pixel_pitch** - rozmiar piksela SLM
- **wavelength** - długość fali
- **distanceZ** - odległość propagacji hologram - obiekt
- **img_width**, **img_height** - wymiary SLM
- **generate_hologram** i **optimize_gs** - boolean zmienia tryb pracy (generate_hologram na true tworzy hologram z chmury punktów; optimise_gs włącza algorytm Gerchberga-Saxona)
- img_path - ścieżka nowo-utworzonego hologramu

```
save_dir = '/content/drive/MyDrive/pointcloud/params'

  

# Create the directory if it doesn't already exist

os.makedirs(save_dir, exist_ok=True)

  

file_path = os.path.join(save_dir, 'parameters.json')

  

# 1. Define your program parameters in a dictionary

parameters = {

    "pixel_pitch": 3.74e-6,

    "wavelength": 632.8e-9,

    "distanceZ": 0.9995,

    "img_width": 3840,

    "img_height": 2180,

    "target_vram_gb": 10.0,

    "generate_hologram": True,

    "optimize_gs": True,

    "img_path": "/content/drive/MyDrive/pointcloud/CGHs/zmienNazwe.png",

}

  

# 2. Save the parameters to a JSON file

with open(file_path, "w") as json_file:

    # indent=4 formats the file to be easily readable by humans

    json.dump(parameters, json_file, indent=4)

  

print("Parameters successfully saved to: ",file_path)
```

```
params_path = '/content/drive/MyDrive/pointcloud/params/parameters.json'

  

with open(params_path, "r") as json_file:

    loaded_params = json.load(json_file)

  

# Load the global program parameters

  

pixel_pitch = loaded_params["pixel_pitch"]

wavelength = loaded_params["wavelength"]

distance = loaded_params["distanceZ"]

width = loaded_params["img_width"]

height = loaded_params["img_height"]

target_vram_gb = loaded_params["target_vram_gb"]

generate_hologram = loaded_params["generate_hologram"]

optimize_gs = loaded_params["optimize_gs"]

img_path = loaded_params["img_path"]

  

# instrukcja warunkowa optymalizacji GS

if generate_hologram != True:

    optimize_gs = False

    print("Tryb odczytu jest włączony. Ustaw generate_holograms na TRUE")
```

