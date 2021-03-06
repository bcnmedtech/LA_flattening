# Left atrial flattening
Author: Marta Nuñez-Garcia (marnugar@gmail.com)

## About
Implementation of the Left Atrial (LA) Fast Regional Flattening (FRF) method described in:
[*Fast quasi-conformal regional flattening of the left atrium*. Marta Nuñez-Garcia, Gabriel Bernardino, Francisco Alarcón, Gala Caixal, Lluís Mont, Oscar Camara, and Constantine Butakoff. arXiv preprint arXiv:1811.06896, (2018)](https://arxiv.org/pdf/1811.06896.pdf). Please cite this reference when using this code.

Example:

![Example image](https://github.com/martanunez/LA_flattening/blob/master/example_im.png)

## Code
[Python](https://www.python.org/) scripts depending (basically) on [VTK](https://vtk.org/) and [VMTK](http://www.vmtk.org/). 

## Pipeline
The pipeline is split in 4 parts. You can skip the first ones depending on your input data.


- **1_mesh_standardisation:** standardises LA mesh, i.e. clip pulmonary veins (PVs), left atrial appendage (LAA), and mitral valve (MV). Launches GUI and asks the user to select 5 seeds close to the ending points of the 4 PVs and LAA. It returns a clipped mesh and auxiliary files containing info about seeds, clipping planes, etc. This script is adapted from [run_standardization](https://github.com/catactg/SUM) by Catalina Tobon Gomez. 
- **2_close_holes_project_info:** Closes holes corresponding to clipped PVs and LAA. Hole filling is done with [Butakoff's implementation](https://github.com/cbutakoff/tools/tree/master/FillSurfaceHoles) of the method published in P. Liepa "Filling Holes in Meshes", 2003. The binary file is included in this repository, you may need to provide execution permission (*chmod +x FillSurfaceHoles*). Hole filling can also be done manually with [reMESH.](http://remesh.sourceforge.net/) This script also marks the filled holes with a scalar array and additionally, transfers all scalar arrays in the input mesh to the output (closed) mesh.
- **3_divide_LA:** Parcellates mesh creating appropriate paths to divide the LA in the 5 pieces considered in our regional flattening. Launch GUI and ask the user to select the 9 required seeds. See seeds order here:
![Example image](https://github.com/martanunez/LA_flattening/blob/master/im_flat.png)
- **4_flat_atria:** Quasi-conformal LA regional flattening. Given a LA mesh with clipped & filled holes (PVs, LAA) and only 1 hole corresponding to the MV, it returns a flat (2D) version of the input mesh. Implementation of a conformal flattening considering 6 boundaries (4 PVs + LAA + MV) and the additional regional constraints (division lines) obtained in the previous step.

## Instructions
Clone the repository:
```
git clone https://github.com/martanunez/LA_flattening

cd LA_flattening
```

## Usage
```
python 1_mesh_standardisation.py input_mesh dist_pv dist_LAA maxslope clspacing skippointsfactor highslope bumpcriterion pvends vis save 

python 2_close_holes_project_info.py clipped_mesh clipped_mitral_mesh clipped_closed_mesh

python 3_divide_LA.py clipped_closed_mesh

python 4_flat_atria.py clipped_closed_mesh
```

## Usage example
```
python 1_mesh_standardisation.py data/mesh.vtk 3 3 5 0.4 0.1 1.2 0.05 1 1 0

python 2_close_holes_project_info.py data/mesh_crinkle_clipped.vtk data/mesh_clipped_mitral.vtk data/mesh_clipped_c.vtk

python 3_divide_LA.py data/mesh_clipped_c.vtk

python 4_flat_atria.py data/mesh_clipped_c.vtk
```

## Dependencies
The scripts in this repository were successfully run with:
1. Ubuntu 16.04
    - [Python](https://www.python.org/) 2.7.12
    - [VMTK](http://www.vmtk.org/) 1.4
    - [VTK](https://vtk.org/) 8.1.0
2. Windows 8.1
    - [Python](https://www.python.org/) 3.6.4
    - [VMTK](http://www.vmtk.org/) 1.4
    - [VTK](https://vtk.org/) 8.1.0
  
Other required packages are: NumPy, SciPy, Matplotlib, joblib, and python-tk.  

### Python packages installation
To install VMTK follow the instructions [here](http://www.vmtk.org/download/). The easiest way is installing the VMTK [conda](https://docs.conda.io/en/latest/) package (it additionally includes VTK, NumPy, etc.). It is recommended to create an environment where VMTK is going to be installed and activate it:

```
conda create --name vmtk_env
source activate vmtk_env
```
Then, install vmtk:
```
conda install -c vmtk vtk itk vmtk
```
Activate the environment when needed using:
```
source activate vmtk_env
```
You can also build VMTK from source if you wish, for example, to use a specific VTK version. Instructions can be found [here.](http://www.vmtk.org/download/)


## License
The code in this repository is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

This code is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details: [http://www.gnu.org/licenses/](http://www.gnu.org/licenses/)
