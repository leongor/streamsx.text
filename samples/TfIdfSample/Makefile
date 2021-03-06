# Licensed Materials - Property of IBM
# Text Toolkit for IBM Streams
# (c) Copyright IBM Corp. 2016
# All rights reserved.                                
.PHONY: build all clean distributed

# Fill in streamsx.text toolkit location here.
STREAMSX_TEXT_TOOLKIT ?= ../../com.ibm.streamsx.text

SPLC = $(STREAMS_INSTALL)/bin/sc
SPLC_FLAGS ?= -a  
SPL_MAIN_COMPOSITE = TfIdfSample
SPL_MAIN_COMPOSITE_MODEL_BUILDER = IdfCorpusTrainer
SPL_DATA_DIR = ./data
SPL_CMD_ARGS ?= -t $(STREAMSX_TEXT_TOOLKIT)

### determine 'streamtool'
ifneq ($(realpath $(STREAMS_INSTALL)),)
ST=$(STREAMS_INSTALL)/bin/streamtool
else 
$(error STREAMS_INSTALL not defined)
endif
STANDALONE_FLAG =
DISTRIBUTED_BUILD_MODEL_BUILDER =
STANDALONE_REQUIRED = $(shell $(ST) version | perl -ne 'm/^Version=(.*)/ && (print ($$1 =~ m/^3\.2\.[01]\./ ? "yes" : "no"));')
ifeq ("$(STANDALONE_REQUIRED)","no")
STANDALONE_REQUIRED = $(shell $(ST) version | perl -ne 'm/^Version=(.*)/ && (print ($$1 =~ m/^4\.[01]\./ ? "yes" : "no"));')
endif
ifeq ("$(STANDALONE_REQUIRED)","yes")
STANDALONE_FLAG = -T
DISTRIBUTED_OUTPUT_DIR = output/Distributed
DISTRIBUTED_BUILD = $(SPLC) $(SPLC_FLAGS) -M $(SPL_MAIN_COMPOSITE) --data-directory $(SPL_DATA_DIR) $(SPL_CMD_ARGS) --output-directory $(DISTRIBUTED_OUTPUT_DIR)
DISTRIBUTED_BUILD_MODEL_BUILDER = $(SPLC) $(SPLC_FLAGS) -M $(SPL_MAIN_COMPOSITE_MODEL_BUILDER) --data-directory $(SPL_DATA_DIR) $(SPL_CMD_ARGS) --output-directory $(DISTRIBUTED_OUTPUT_DIR)/ModelBuilder
else
DISTRIBUTED_BUILD = echo "no extra build required for distributed"
endif

build: standalone 

all: clean build

distributed:
	$(DISTRIBUTED_BUILD)
	$(DISTRIBUTED_BUILD_MODEL_BUILDER)

standalone:
	$(SPLC) $(SPLC_FLAGS) $(STANDALONE_FLAG) -M $(SPL_MAIN_COMPOSITE) --data-directory $(SPL_DATA_DIR) $(SPL_CMD_ARGS)
	$(SPLC) $(SPLC_FLAGS) $(STANDALONE_FLAG) -M $(SPL_MAIN_COMPOSITE_MODEL_BUILDER) --data-directory $(SPL_DATA_DIR) $(SPL_CMD_ARGS) --output-directory output/ModelBuilder

clean: 
	$(SPLC) $(SPLC_FLAGS) -C -M $(SPL_MAIN_COMPOSITE)
	$(SPLC) $(SPLC_FLAGS) -C -M $(SPL_MAIN_COMPOSITE_MODEL_BUILDER)
	rm -rf output
	rm -rf bin
	rm -f data/out*

