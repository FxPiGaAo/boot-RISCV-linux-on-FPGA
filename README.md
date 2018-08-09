# boot-RISCV-linux-on-FPGA

## steps to boot riscv-linux on fpga

### 1. generate the verilog file:
      make -f Makefile.u500vc707devkit verilog
note: before generating the file, we need to modify the scala configuration file first.


### 2. modify the verilog file


### 3. generate the mcs file:
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

#### 5. Click OK to “Do you want to program the configuration memory device now ?”
#### 6. Add freedom-u500-vc707-0-1.mcs and freedom-u500-vc707-0-1.prm
#### 7. Select RS Pins = 25:24
#### 8. Select OK
Programming will take approximately five minutes.
