all: simulate

NPROC = $$((`nproc`-1))
NPROC = 16

# -------------------------------------
# Testbench setup
# -------------------------------------
VERILATOR := verilator
ifdef VERILATOR_ROOT
VERILATOR := $(VERILATOR_ROOT)/bin/verilator
endif

UVM_ROOT ?=../../../../uvm
UVM_TEST ?= apb2_master_rand_test

VERILOG_DEFINE_FILES = ${UVM_ROOT}/src/uvm.sv -f flist 

VERILOG_INCLUDE_DIRS = ../tb_src ../test ${UVM_ROOT}/src

# -------------------------------------
# Compilation/simulation configuration
# -------------------------------------
SIM_NAME ?= apb
SIM_DIR := $(SIM_NAME)-sim
COMPILE_ARGS += -DUVM_NO_DPI
COMPILE_ARGS += -DAF_NO_UVM_RAL
COMPILE_ARGS += -fno-gate
COMPILE_ARGS += --prefix $(SIM_NAME) -o $(SIM_NAME)
COMPILE_ARGS += $(addprefix +incdir+, $(VERILOG_INCLUDE_DIRS))
EXTRA_ARGS += --timescale 1ns/1ps --error-limit 100
WARNING_ARGS += -Wno-lint \
	-Wno-style \
	-Wno-SYMRSVDWORD \
	-Wno-IGNOREDRETURN \
	-Wno-CONSTRAINTIGN \
	-Wno-ZERODLY \
	-Wno-REDEFMACRO \
	-Wno-INITIALDLY

COMP_LOG_ARGS = 2>&1 | tee comp.log
RUN_LOG_ARGS = 2>&1 | tee run.log

# -------------------------------------
# Make UVM test with Verilator
# -------------------------------------
$(SIM_DIR)/$(SIM_NAME).mk: $(wildcard hdl/*.sv)
	$(VERILATOR) --cc --exe --main --timing -Mdir $(SIM_DIR) \
	${COMPILE_ARGS} ${EXTRA_ARGS} \
	${VERILOG_DEFINE_FILES} \
	${WARNING_ARGS} ${COMP_LOG_ARGS}

$(SIM_DIR)/$(SIM_NAME): $(SIM_DIR)/$(SIM_NAME).mk
	$(MAKE) -j${NPROC} -C $(SIM_DIR) $(BUILD_ARGS) -f $(SIM_NAME).mk

simulate: $(SIM_DIR)/$(SIM_NAME).mk $(SIM_DIR)/$(SIM_NAME)
	$(SIM_DIR)/$(SIM_NAME) +UVM_TESTNAME=$(UVM_TEST) ${RUN_LOG_ARGS}

clean:
	rm -rf simv*.daidir csrc
	rm -rf csrc* simv*
	rm -rf $(SIM_DIR)


.PHONY: simulate clean
