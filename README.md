# Spatial Molding for Approachable Rigid Targets (SMART)
<img width="300" alt="image" src="https://github.com/SigmanGroup/SMART-molecular-descriptors/assets/84196711/6da48469-0a27-4084-bf85-a8d474757127"> <img width="300" alt="image" src="https://github.com/SigmanGroup/SMART-molecular-descriptors/assets/84196711/84905748-9780-4411-a9cb-8d97dcc63e2a">

An open-source Python package for generation of SMART probe ensembles and calculation of SMART molecular descriptors.

## Dependencies
- pandas
- scipy
- mathutils
- argparse
- RDKit
- morfeus (optional)
- dbstep (optional)
- pyvista (optional)
- chimera (optional)

# Instructions
## Step 1: Set up the System for Analysis
#### Reading a structure
```
import SMART as smart

# read from a file (.pdb, .sdf, .mol2, or .xyz)
structure = smart.ReadFile(path)

# or pass MOL directly from RDKit
structure = smart.ReadMol(mol)
```

#### Define a reference binding vector
```
# multiple reference vector computations are supported,
# select one from below

# define a linear reference vector from atom ids
binding_atom = 0
ref_atom = 1
structure.reference_vector(tip_id=binding_atom, ref_id=ref_atom, dist=2.0)

# define a linear reference vector from atom positions
binding_atom = np.array([0,0,0])
ref_atom = np.array([0,0,1])
structure.reference_vector(tip_pos=binding_atom, ref_pos=ref_atom, dist=2.0)

# define a cross-product reference vector from atom positions
# (follows the right-hand-rule)
structure.reference_angle(tip_pos=binding_atom, ref_pos=ref_atom, dist=2.0)

# define a geometry and find empty coordination site
binding_atom = np.array([0,0,0])
ref_atom1, ref_atom2 = 1, 2
structure.reference_geometry(tip_pos=binding_atom, refs_ids=[ref_atom1, ref_atom2], geom='trigonal', dist=2.0)

# (Optional: for debugging)
# Export .pdb file with a dummy atom at the computed binding site
structure.export_alignment(out=file.split('.')[0]+'_align', dummy='X')
```

## Step 2: Run Molecular Probe Conformational Sampling
#### Read probe file and find template
```
from SMART import conf_search as search

# set name of probe .mol2 file (default probes in folder /Probes/)
probe_name = 'S_SiH2_12_cyclic'
if search.TEMPLATE.is_template(probe_name):
      # perform free conformer search to make template
      print('get template')
      search.TEMPLATE.GetTemplate(probe_name)
  else:
      # load pre-existing template
      print('gen template')
      search.TEMPLATE.GenerateTemplate(probe_name)
```

#### Set conformational search parameters and perform template search
```
# performs 50 steps of fitting
search.PARAMS.read_parameters({'NSTEP':50, 'VERBOSE':True})

# !! start the search !!
ensemble = search.TEMPLATE_SEARCH(structure) # output conformers

# (Optional: export ensemble to single .pdb file)
smart.ExportStructure(ensemble, 'SMART_out')
```

## Step 3: Compute SMART Descriptors
```
from SMART import descriptors as desc

# compute SMART descriptors using triangulation
properties_tri = desc.get_Cloud_Properties(structure.MOL, ensemble, id=binding_atom, prox_radius=4.0, alpha=0)

# compute SMART descriptors using Buried Volume
properties_tri = desc.get_BuriedVolume_Properties(structure.MOL, ensemble, id=binding_atom, prox_radius=4.0)
```

Alternatively, compute SMART descriptors using UCSF Chimera through the script ```chimera_descriptors.py```

See documentation for more information.


# Citations
This package:
- pending...

Literature Using SMART:
- Cammarota, R. C., Liu, W., Bacsa, J., Davies, H. M. L., & Sigman, M. S. Mechanistically Guided Workflow for Relating Complex Reactive Site Topologies to Catalyst Performance in C-H Functionalization Reactions. Journal of the American Chemical Society, 2022, 144(4), 1881–1898. [https://doi.org/10.1021/jacs.1c12198](https://doi.org/10.1021/jacs.1c12198)
- Lucas W. Souza, Beck R. Miller, Ryan C. Cammarota, Anna Lo, Ixchel Lopez, Yuan-Shin Shiue, Benjamin D. Bergstrom, Sarah N. Dishman, James C. Fettinger, Matthew S. Sigman, and Jared T. Shaw, ACS Catalysis, 2024, 14 (1), 104-115, [4256](https://doi.org/10.1021/acscatal.3c04256)https://doi.org/10.1021/acscatal.3c04256
