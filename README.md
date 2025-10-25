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
$ mkdir build
$ cd build
$ cmake -DBUILD_DEPS=ON -DCMAKE_BUILD_TYPE=Release ..
$ make -j$(nproc)
$ sudo make install

```

### 2️⃣ Step3 Build and Install `spdlog` tool (Critical Dependency)
``` bash
# installation outside openroad folder
$ cd ~
$ git clone -b v1.11.0 [https://github.com/gabime/spdlog.git](https://github.com/gabime/spdlog.git)
$ cd spdlog
$ cmake -DCMAKE_BUILD_TYPE=Release -DSPDLOG_FMT_EXTERNAL=OFF .
$ make -j$(nproc)
$ sudo make install
```

#### 👿 Carefully Patch OpenROAD Source Files to Link spdlog
- Because we manually cloned spdlog (and commented out the system find_package), we must patch two files to tell CMake how to link the internal files directly
- `src/CMakeLists.txt` (To stop searching for system package)
- Open the file: sudo gedit `src/CMakeLists.txt`
- Find the line find_package(spdlog REQUIRED) (around line 235) and comment it
- #### 🅱️ part
- Patch Root `CMakeLists.txt` (To build the dependency target)
- Open the file: `sudo gedit CMakeLists.txt`
- Add the line `add_subdirectory(third-party/spdlog)` immediately after the existing `add_subdirectory(third-party)` line, and before `add_subdirectory(src)`.
```bash
# --- ADD THIS LINE immediately after the existing `add_subdirectory(third-party)` ---
add_subdirectory(third-party/spdlog)
```

### 2️⃣ Step4 Build and Install gtest an dependency.

``` bash
# just move to the below folder and do cmake and make that will copy gtest and gmonk in the `usr/lib`
$ cd /usr/src/gtest
$ sudo cmake .
$ sudo make
$ sudo cp lib/libgtest*.a /usr/lib/

```

**ScreenShot:** The Dependencies all met.

<img width="841" height="682" alt="final" src="https://github.com/user-attachments/assets/73e93921-487d-43f3-a91c-008fd088cbb8" />


### 2️⃣ Step5 Build and Install ⚡ OpenROAD clone
✋ note- we will clone the openroad and built seperatly
- These steps ensure a complete and clean repository, resolving initial build issues like missing submodules.
- Clone OpenROAD and Fix Submodule Corruption  `git submodule update --init --recursive `
- This ensures all required third-party libraries (like spdlog and sta) are fully downloaded.
``` bash
# Clone OpenROAD repository (replace <path> with your chosen directory)
$ cd $HOME/vsdflow git clone [https://github.com/The-OpenROAD-Project/OpenROAD.git](https://github.com/The-OpenROAD-Project/OpenROAD.git)
$ cd OpenROAD
$ mkdir build
$ cd build
# Run CMake (using g++-9 and pointing to OR-Tools)
cmake .. -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_FLAGS="-Wno-error"
or
cmake .. \
-DCMAKE_BUILD_TYPE=Release \
-DCMAKE_CXX_FLAGS="-Wno-error" \
-DCMAKE_PREFIX_PATH="/usr/local" \
-DCMAKE_CXX_COMPILER=/usr/bin/g++-9
```
**ScreenShot:** The picture shows that openroad cmake is complete. 
<img width="1081" height="644" alt="final1" src="https://github.com/user-attachments/assets/76bfe8e3-0f3f-48f2-9478-2680b2ce69db" />

``` bash
# 🕡 the cmake will take enough time to complete 
# Compile and Install OpenROAD
$ <Build Folder path > make -j$(nproc)
$ sudo make install
```
**ScreenShot:** The picture shows that openroad after Make and install.

<img width="1081" height="644" alt="final1" src="https://github.com/user-attachments/assets/5a61693a-3c76-4f8e-b69a-c256a8834236" />

**ScreenShot:** The picture shows that openroad after Make 100% done!!.
<img width="1416" height="1011" alt="doneopenroad" src="https://github.com/user-attachments/assets/873332ff-6d65-4735-90cf-33d3bf17f561" />


# Successfully Installed

**ScreenShot:** The picture shows that openroad is successfully Installed. 
<img width="1265" height="972" alt="make_install" src="https://github.com/user-attachments/assets/01bb32e3-663a-43bf-8980-8f7bb9e41414" />


**ScreenShot:** The picture shows that openroad is successfully Installed on Ubuntu 24 WSL.



### 👏after these all run command openroad and it will open like this -

<img width="1140" height="240" alt="image" src="https://github.com/user-attachments/assets/cd37c721-f27a-4569-8683-d9db759c146b" />


**ScreenShot:** The picture shows that openroad test folder contains PDK and example which we will take in PART-2

<img width="1840" height="553" alt="ls_test_lib" src="https://github.com/user-attachments/assets/5d8f0d38-6bd8-4b0c-a034-ed15856d2944" />


#  Acknowledgement 
1. VSD and Kunal Sir
2. General Chat VSD
3. Chat GPT and all Internet user working on OPENROAD Installation.
4. ALL friends.
