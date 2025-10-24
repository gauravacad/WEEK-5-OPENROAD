# WEEK-5-OPENROAD-readme.md
# Installation Openroad on Ubuntu 24




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

**ScreenShot:** The picture shows that openroad GUI is successfully Mapped
<img width="1860" height="966" alt="image" src="https://github.com/user-attachments/assets/9bf5231e-ec0a-4e5b-9aba-51e1f9171cdc" />
