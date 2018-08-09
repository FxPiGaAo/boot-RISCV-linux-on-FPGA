# boot-RISCV-linux-on-FPGA

steps to boot riscv-linux on fpga

1. generate the verilog file:
      make -f Makefile.u500vc707devkit verilog
note: before generating the file, we need to modify the scala configuration file first.


2. modify the verilog file


3. generate the mcs file:
here's the variable we might need to modify:
      1) the VSRCS varible which points to the verilog file path in the makefile.
      
      2) in those following vivado tcl command in common.mk file, we might need to modify varable MODEL(U500VC707DevKitFPGAChip) and variable BOARD(vc707) directly. Then, we will get the right bitstream.
         cd $(BUILD_DIR); vivado \
		           -nojournal -mode batch \
		           -source $(fpga_common_script_dir)/vivado.tcl \
		           -tclargs \
		           -top-module "$(MODEL)" \
		           -F "$(f)" \
		           -ip-vivado-tcls "$(shell find '$(BUILD_DIR)' -name '*.vivado.tcl')" \
		           -board "$(BOARD)"
       
 But I'm not so sure since I am not very familiar with the vivado tcl command.
       
       3)in the following vivado tcl command(this command transfers the bitstream into the mcs file), we also might need to use our board parameter to replace $(BOARD):
          vivado -nojournal -mode batch -source $(fpga_common_script_dir)/write_cfgmem.tcl -tclargs $(BOARD) $@ $<
          
