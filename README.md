# boot-RISCV-linux-on-FPGA

## steps to boot riscv-linux on fpga

### 1. Generate the verilog file:
      make -f Makefile.u500vc707devkit verilog
note: before generating the file, we need to modify the scala configuration file first.


### 2. Modify the verilog file


### 3. Generate the mcs file:
here's the variable we might need to modify:
#### 1) the VSRCS varible which points to the verilog file path in the makefile.
#### 2) in those following vivado tcl command in common.mk file, we might need to modify varable MODEL(U500VC707DevKitFPGAChip) and variable BOARD(vc707) directly. But I'm not so sure since I am not very familiar with the vivado tcl command. Then, we will get the right bitstream.
      
      cd $(BUILD_DIR); vivado \
		           -nojournal -mode batch \
		           -source $(fpga_common_script_dir)/vivado.tcl \
		           -tclargs \
		           -top-module "$(MODEL)" \
		           -F "$(f)" \
		           -ip-vivado-tcls "$(shell find '$(BUILD_DIR)' -name '*.vivado.tcl')" \
		           -board "$(BOARD)"

#### 3) in the following vivado tcl command(this command transfers the bitstream into the mcs file), we also might need to use our board parameter to replace $(BOARD):
          
	vivado -nojournal -mode batch -source $(fpga_common_script_dir)/write_cfgmem.tcl -tclargs $(BOARD) $@ $<
          
### 4. To program the flash with Vivado :
#### 1. Launch Vivado
#### 2. Open Hardware Manager, open target board
#### 3. Right click on the FPGA device and select “Add Configuration Memory Device”
#### 4. Select
	Part			mt28gu01gaax1e-bpi-x16
	Manufacturer		Micron
	Alias			28f00ag18f 
	Family			bpi
	Type			g18
	Density			1024
	Width			x16
This parameter is only for board vc707. For the specific procedure, please refer to: https://www.xilinx.com/support/documentation/sw_manuals/xilinx2018_2/ug908-vivado-programming-debugging.pdf#nameddest=xGenerateBitstreamsForUseWithConfigurationMemoryDevices
in chapter 6.

#### 5. Click OK to “Do you want to program the configuration memory device now ?”
#### 6. Add freedom-u500-vc707-0-1.mcs and freedom-u500-vc707-0-1.prm
#### 7. Select RS Pins = 25:24
#### 8. Select OK
Programming will take approximately five minutes.
### 5. Prepare a complete SD card image :
The Freedom U500 Software Development Kit provides everything required to compile and cus-
tomize this SD Card image - GCC 6.1.0 cross compilation toolchain, Linux 4.6.2 kernel and build-
root managed root filesystem.
To clone the Freedom U500 SDK git repository :

    git clone --recursive https://github.com/sifive/freedom-u-sdk.git
To prepare a complete SD card image :

    cd freedom-u-sdk; make
To load the compiled image to the SD card :

    dd if=work/bbl.bin of=/dev/your-sd-card bs=1M
