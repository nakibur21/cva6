# Copyright 2021 Thales DIS design services SAS
#
# Licensed under the Solderpad Hardware Licence, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# SPDX-License-Identifier: Apache-2.0 WITH SHL-2.0
# You may obtain a copy of the License at https://solderpad.org/licenses/
#
# Original Author: Jean-Roch COULON (jean-roch.coulon@thalesgroup.com)
#

DESIGN_NAME           = ariane
PERIOD               ?= 17
FOUNDRY_PATH         ?=
TECH_NAME            ?=
TARGET_LIBRARY_FILES  = $(TECH_NAME).db
INPUT_DELAY          ?= 0.46
OUTPUT_DELAY         ?= 0.11
DC_SHELL_PATH        ?= /opt/synopsys/syn/Q-2019.12/bin/
NAND2_AREA           ?= 1120
TARGET               ?= cv64a6_imafdc_sv39

EXPORT_LIST=SNPSLMD_QUEUE=TRUE TECH_NAME=$(TECH_NAME) DESIGN_NAME=$(DESIGN_NAME) TERM=vt100 PERIOD=$(PERIOD) FOUNDRY_PATH=$(FOUNDRY_PATH) TARGET_LIBRARY_FILES=$(TARGET_LIBRARY_FILES) INPUT_DELAY=$(INPUT_DELAY) OUTPUT_DELAY=$(OUTPUT_DELAY)

ifndef FOUNDRY_PATH
  $(error "Please provide FOUNDRY techno")
endif
ifndef TECH_NAME
  $(error "Please provide TECH_NAME techno")
endif

pre_cva6_synth:
	grep "CVA6_REPO_DIR\}" ../../core/Flist.$(TARGET)|grep -v "instr_tracer"|grep -v "incdir" > Flist.cva6_synth
	sed -i "s/^/analyze -f sverilog -define {WT_DCACHE,RVFI_TRACE} -lib ariane_lib /" Flist.cva6_synth

cva6_synth: pre_cva6_synth
	@echo $(PERIOD)
	@export $(EXPORT_LIST); $(DC_SHELL_PATH)/dc_shell -f ./cva6_synth.tcl -output synthesis_batch.log
	python scripts/gate_analysis.py '$(DESIGN_NAME)/reports/$(PERIOD)/$(DESIGN_NAME)_$(TECH_NAME)_synth_area.rpt' $(NAND2_AREA)
	sed 's/tc_sram_256_64_00000008_00000001_00000001_none_0 gen/tc_sram gen/' ariane_synth.v > ariane_synth_modified.v

cva6_read:
	@export $(EXPORT_LIST); $(DC_SHELL_PATH)/dc_shell -f cva6_read.tcl -gui

clean:
	@rm -rf work alib* *_LIB *log *svf netlist reports ${DESIGN_NAME}
