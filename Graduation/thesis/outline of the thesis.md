this project is the outline for my [[graduation thesis]]. in here I want to present the scope of my project in the most design - accurate way possible. Here I want to facilitate the method and correct flaws of the proposed thesis. 

---
### expectations
In the notes below I will place the assumed **table of contents**, **flowchart of my work**, theoretical **mathematical equations** and explanations, results and evaluation and finally conclusion and future work.

---

##### from cover to cover
here there must be no mistakes. from the beginning to end everything about my project must be included in my work. 
- **Introduction** - state the scope and objectives, and why CGH matters today
- **The fundaments** (everything) to understanding holography and it's meaning and use in AR/VR systems. 
- Current **state of the art CGH algorithms** should be compared, to briefly touch how 3D scenes are represented in computer generated holography. 
- My unique **proposed algorithm**. Dive into how your design works. Cover the finished (and unfinished) code. Walk through the optimization strategy for time reduction and quality improvement. Create a flowchart, a visual diagram showing the data pipeline. This is an amazing diagram that shows how the data moves and changes in your algorithm.
- My own python **codebase**. The enormous software architecture can be broken down into an understandable document. Here it is that all the modules, classes, and visual data pipelines should be explained, for a dummy to understand. This is a document necessary to present the transformation of physical into virtual 3D scene. The proposed key libraries are the building blocks, the use of them must be discussed in the paper. As well as the limits of their use, these challenges (such as memory limits) must be explained and settled - such as performance bottlenecks, broadcasting solutions. 
- **Results and evaluation** - describe the experiment from beginning to end. Describe the proposed input (images, parameters f.e. distance, wavelength) used for testing. Explain the achieved output (reconstruction - both numerical and optical, execution time per batch, general time, iterations (if GS algorithm stays)). Engineers love metrics so they must appear here, so evaluate the reconstruction quality using metrics like Peak Signal-to-noise ratio, structural similarity index (SSIM) or diffraction efficiency
- **Conclusion** - did I meet the objectives outlined in the introduction? say yes, but be honest about the limitations (f.e. is to slow for real-time video). What could happen in the future to your algorithm to its improvement?


#### pytanie do promotora
> mój projekt tworzy scenę 3D reprezentowaną w jednostkach metrycznych. z tej sceny tworzy układ symulacji światła odbitego z tego obiektu, aby utworzyć wirtualne pole falowe i następnie wirtualny interferogram. Czy moja praca powinna skupić się bardziej na matematycznym opisie sceny czy na kodowym opisie algorytmu?


