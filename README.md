# HDF5 External Compression Filter (ECF) Plugin Installation Guide

This guide provides step-by-step instructions to install the HDF5 External Compression Filter (ECF) plugin in a micromamba environment.

## Prerequisites

- **Micromamba** installed on your system
- A micromamba environment with Python 3.10+

## 1. Create and Activate Micromamba Environment

If you don't have an environment yet:

```bash
micromamba create -n my_hdf5_env -c conda-forge python=3.10 h5py hdf5
micromamba activate my_hdf5_env
```

If you already have an environment:

```bash
micromamba activate my_hdf5_env
```

## 2. Install Required Packages

Ensure `h5py` and `hdf5` are installed from conda-forge:

```bash
micromamba install -c conda-forge h5py hdf5
```

Verify `h5py` is linked to the shared HDF5 library:

```bash
python -c "import h5py.h5; print(h5py.h5.__file__)"
ldd $(python -c "import h5py.h5; print(h5py.h5.__file__)") | grep hdf5
```

You should see `libhdf5.so` linked from your micromamba environment.

## 3. Clone and Build the Plugin

```bash
git clone https://github.com/Rzalnjd/hdf5-ecf.git
cd hdf5-ecf
mkdir build && cd build

cmake .. \
  -DCMAKE_INSTALL_PREFIX="$CONDA_PREFIX" \
  -DHDF5_ROOT="$CONDA_PREFIX" \
  -DBUILD_TESTING=OFF

make -j4
make install
```

This installs the plugin into `$CONDA_PREFIX/lib/hdf5/plugin`.

## 4. Set the HDF5 Plugin Path

To load the plugin, set the `HDF5_PLUGIN_PATH` environment variable:

```bash
export HDF5_PLUGIN_PATH="$CONDA_PREFIX/lib/hdf5/plugin"
```

To make this persistent:

```bash
mkdir -p "$CONDA_PREFIX/etc/conda/activate.d"
echo 'export HDF5_PLUGIN_PATH="$CONDA_PREFIX/lib/hdf5/plugin"' > "$CONDA_PREFIX/etc/conda/activate.d/hdf5_plugin.sh"
```


