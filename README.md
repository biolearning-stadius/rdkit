# RDKit fork

This is a fork from [rdkit/rdkit](https://github.com/rdkit/rdkit). This fork adds the possibility to produce detailed labelling information for 2D images of chemical compounds produced by RDkit. This can be useful for machine learning tools like [ChemGrapher] (https://arxiv.org/abs/2002.09914).
To make use of this fork you can find below some installation instructions and example code.
However the official repository README you can find [here](https://github.com/rdkit/rdkit).

## Installation

First create your conda environment and install the required packages:

```
conda install -y cmake cairo pillow eigen pkg-config
conda install -y boost-cpp boost py-boost
conda install -c conda-forge xorg-libxrender
conda install -c conda-forge xorg-libxext
conda install -c conda-forge xorg-libxau
conda install -y gxx_linux-64
```

Clone this repo, create build folder and start to build from sources:

```
git clone https://github.com/biolearning-stadius/rdkit.git
cd rdkit
mkdir build && cd build
cmake -DPy_ENABLE_SHARED=1 \
  -DRDK_INSTALL_INTREE=ON \
  -DRDK_INSTALL_STATIC_LIBS=OFF \
  -DRDK_BUILD_CPP_TESTS=ON \
  -DPYTHON_NUMPY_INCLUDE_PATH="$(python -c 'import numpy ; print(numpy.get_include())')" \
  -DBOOST_ROOT="$CONDA_PREFIX" \
  -D RDK_BUILD_CAIRO_SUPPORT=ON \
  ..
make
make install
ctest
```

Some necessary configuration steps:

```
export RDBASE=<install-dir>/rdkit/
export LD_LIBRARY_PATH=$RDBASE/lib
export PYTHONPATH=$RDBASE

conda env config vars set RDBASE=<install-dir>/rdkit/
conda env config vars set LD_LIBRARY_PATH=<install-dir>/rdkit/lib
conda env config vars set PYTHONPATH=<install-dir>/rdkit/
```

## Example Code

```python
from rdkit import Chem
from rdkit.Chem.Draw import rdMolDraw2D
import os
os.environ["RDKIT_LABELPATH"] = "labels_rdkit" 
mol = Chem.MolFromSmiles('Cl[C@H](F)NC\C=C\C')
d = rdMolDraw2D.MolDraw2DCairo(1000,1000)
d.DrawMolecule(mol)
d.FinishDrawing()
with open('0.png', 'wb') as f:   
   f.write(d.GetDrawingText())
```
Now the labels should be in the `labels_rdkit` file.
