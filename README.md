# WEEK-5-OPENROAD-readme.md
# ⏬ Installation OPENROAD on Ubuntu 24
- OpenROAD is the core application and the engine of the flow. It is a unified executable that performs the actual physical design work.
- OpenROAD (Open Rapid Object-Oriented Design) is an open-source, fully autonomous, end-to-end RTL-to-GDSII digital physical design toolchain developed primarily for ASIC implementation.
- Its objective is to enable push-button, timing-closed layout generation without proprietary software dependencies.
- OpenROAD integrates multiple components that collectively perform synthesis, floorplanning, placement, clock tree synthesis, routing, and signoff analysis.
- The tool leverages key open-source engines such as Yosys for logic synthesis, and OpenSTA for static timing analysis.

## 👿 INSTALLATION PART 
- To install the openroad on Ubuntu 24 is liitle bit complex and tricky 👿.

### Follow the Installation order  

### 1️⃣ Step1 Install Necessary Build Tools and GCC-9

- The default compiler version (GCC 11+) often causes internal C++ conflicts with OpenROAD's dependencies. We force the use of GCC-9.
 ```bash
## Update repositories and install the build essentials
$ sudo apt update
$ apt install build-essential git cmake swig python3-dev -y

# Install the compatible compiler (GCC-9)
$ sudo add-apt-repository ppa:ubuntu-toolchain-r/test -y
$ sudo apt update
$ sudo apt install g++-9 -y
```
### 2️⃣ Step2 Build and Install OR-Tools (Critical Dependency)
- Need to build and install OR-Tools to /usr/local so OpenROAD's final configuration can find it.

```bash
# Clone the repository. Have patience it takes almost more than an `hour`.
$ git clone [https://github.com/google/or-tools.git](https://github.com/google/or-tools.git)
$ cd or-tools
# Build and install OR-Tools
$ mkdir build && cd build
$ cmake -DBUILD_DEPS=ON -DCMAKE_BUILD_TYPE=Release ..
$ make -j$(nproc)
$ sudo make install

```



# Navigate back to the parent directory to start OpenROAD clone
cd ../..
```
### 👿note- it is end of phase one and in next phase we will clone the openroad and both tools and openroad should built seperatly.


### 3️⃣step3

- These steps ensure a complete and clean repository, resolving initial build issues like missing submodules.
- Clone OpenROAD and Fix Submodule Corruption
- This ensures all required third-party libraries (like spdlog and sta) are fully downloaded.

```bash
# Clone OpenROAD repository (replace <path> with your chosen directory)
cd /home/iamtheking/Desktop/vlsi/vsd
git clone [https://github.com/The-OpenROAD-Project/OpenROAD.git](https://github.com/The-OpenROAD-Project/OpenROAD.git)
cd OpenROAD

# Manually clone the spdlog repository into the third-party folder
cd third-party
git clone [https://github.com/gabime/spdlog.git](https://github.com/gabime/spdlog.git)
cd ../.. 
```

### 4️⃣step4
#### Patch OpenROAD Source Files to Link spdlog

- Because we manually cloned spdlog (and commented out the system find_package), we must patch two files to tell CMake how to link the internal files directly

- src/CMakeLists.txt (To stop searching for system package)
- Open the file: nano src/CMakeLists.txt
- Find the line find_package(spdlog REQUIRED) (around line 235) and comment it 

#### 🅱️b part

- Patch Root CMakeLists.txt (To build the dependency target)
- Open the file: nano CMakeLists.txt
- Add the line add_subdirectory(third-party/spdlog) immediately after the existing add_subdirectory(third-party) line, and before add_subdirectory(src).

```bash
# --- ADD THIS LINE ---
add_subdirectory(third-party/spdlog)
```


## ⚡phase3

### ✋step5
- do this in the openroad file
- Create Build Directory and Run CMake (The Solution Command)
- This command uses the required flags and compiler path to successfully configure the patched source code.

```bash
rm -rf build
mkdir build
cd build

# Run CMake (using g++-9 and pointing to OR-Tools)
cmake .. \
-DCMAKE_BUILD_TYPE=Release \
-DCMAKE_CXX_FLAGS="-Wno-error" \
-DCMAKE_PREFIX_PATH="/usr/local" \
-DCMAKE_CXX_COMPILER=/usr/bin/g++-9
```
## 🕡 step6
 - Compile and Install OpenROAD

```bash
make -j$(nproc)
sudo make install
```

### 👏after these all run command openroad and it will open like this -

<img width="1229" height="823" alt="Screenshot 2025-10-25 033714" src="https://github.com/user-attachments/assets/2b74dabf-f986-4082-bf4e-ed1f97e57a30" />



















# Successfully Installed 

**ScreenShot:** The picture shows that openroad is successfully Installed on Ubuntu 24 WSL.

<img width="1140" height="240" alt="image" src="https://github.com/user-attachments/assets/cd37c721-f27a-4569-8683-d9db759c146b" />

# Working On First example design Gcd_nand_gate45

```bash
# gcd flow pipe cleaner
source "helpers.tcl"
source "flow_helpers.tcl"
source "Nangate45/Nangate45.vars"

set design "gcd"
set top_module "gcd"
set synth_verilog "gcd_nangate45.v"
set sdc_file "gcd_nangate45.sdc"
set die_area {0 0 100.13 100.8}
set core_area {10.07 11.2 90.25 91}

include -echo "flow.tcl"
```
**ScreenShot:** The picture shows that openroad GUI is successfully running the Gcd_nandgate45.tcl example on Ubuntu 24 WSL.
<img width="870" height="762" alt="image" src="https://github.com/user-attachments/assets/9d075296-cfe2-4c95-a370-616152448835" />

**ScreenShot:** The picture shows that Core Area.
<img width="870" height="762"  alt="image" src="https://github.com/user-attachments/assets/cc21bc84-3d6f-492c-a372-8d80e4ddde4c" />
<img width="870" height="762"  alt="image" src="https://github.com/user-attachments/assets/eed5d953-617d-4bac-8df3-ec65b4c7e066" />


**ScreenShot:** The picture shows that openroad GUI is successfully Mapped
<img width="870" height="762"  alt="image" src="https://github.com/user-attachments/assets/9bf5231e-ec0a-4e5b-9aba-51e1f9171cdc" />
