<link href="./retro.css" rel="stylesheet"></link>

# Build NVIDIA Minkowski Engine on NYU HPC
***Tested in a pip environment.***
## 1. Compile OpenBlas
Download source code from <a href="https://github.com/xianyi/OpenBLAS/releases">https://github.com/xianyi/OpenBLAS/releases</a>, and save it as, say ```OpenBLAS-0.3.18-x64.zip```.

In the unzipped directory, build and install openblas by

```
make BINARY=64 FC=gfortran USE_THREAD=4
make PREFIX=$HOME/openblas install
```
Note that this will install libopenblas in the ```openblas``` directory at $HOME, where you are surely permitted to write and the data will not be flushed.
## 2. Compile numpy with OpenBlas
Download numpy source code from the GitHub repo, say <a href="https://github.com/numpy/numpy/tree/v1.21.4">https://github.com/numpy/numpy/tree/v1.21.4</a>. Unzip it.

Uninstall numpy originally installed by pip:
```
pip uninstall numpy
```
Make a good ```site.cfg``` configuration file by renaming ```site.cfg.example``` in the directory to ```site.cfg``` and edit the file by adding the following lines:
```
[DEFAULT]
library_dirs = /path/to/openblas/lib
include_dirs = /path/to/openblas/include

[atlas]
atlas_libs = openblas
libraries = openblas

[openblas]
libraries = openblas
library_dirs = /path/to/openblas/lib
include_dirs = /path/to/openblas/include
```
If you followed the commands above, ```/path/to/openblas``` should be equal to what you get by interpreting ```$HOME/openblas```.

Build and install numpy with:
```
python setup.py build --fcompiler=gnu95
python setup.py install
```

## 3. Compile Minkowski Engine
***Note: this should be done with a CUDA environment. You may want to switch to a GPU node.***

Setup environments:
```
export CPLUS_INCLUDE_PATH=$C_INCLUDE_PATH:"$HOME/openblas/include"
export C_INCLUDE_PATH=$C_INCLUDE_PATH:"$HOME/openblas/include"
export LD_LIBRARY_PATH="$HOME/openblas/lib":$LD_LIBRARY_PATH
export LIBRARY_PATH="$HOME/openblas/lib":$LIBRARY_PATH
```

Build:
```
pip install -U MinkowskiEngine --install-option="--blas=openblas" --install-option="--blas_library_dirs=/path/to/openblas/lib" -v --no-deps
```
```/path/to/openblas``` should be specified.
