#*******************************************************************************
#  Ledger App
#  (c) 2018 - 2022 Zondax GmbH
#  (c) 2017 Ledger
#
#  Licensed under the Apache License, Version 2.0 (the "License");
#  you may not use this file except in compliance with the License.
#  You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
#  Unless required by applicable law or agreed to in writing, software
#  distributed under the License is distributed on an "AS IS" BASIS,
#  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#  See the License for the specific language governing permissions and
#  limitations under the License.
#*******************************************************************************

ifeq ($(BOLOS_SDK),)
$(error BOLOS_SDK is not set)
endif

MY_DIR := $(dir $(lastword $(MAKEFILE_LIST)))

include $(CURDIR)/../deps/ledger-zxlib/makefiles/Makefile.installer_script

DEFINES       += HAVE_RSA
include $(BOLOS_SDK)/Makefile.defines

#DEFINES += APP_SECRET_MODE_ENABLED
DEFINES += POSTPONE_MAIN_SCREEN_INIT

$(info ************ TARGET_NAME  = [$(TARGET_NAME)])

include $(CURDIR)/../deps/ledger-zxlib/makefiles/Makefile.app_testing

ifndef COIN
COIN=ARWEAVE
endif

include $(CURDIR)/Makefile.version

$(info COIN  = [$(COIN)])

ifeq ($(COIN),ARWEAVE)
# Main app configuration
DEFINES += APP_STANDARD
APPNAME = "Arweave"
APPPATH = "44'/472'"

else
define error_message


COIN value not supported: [$(COIN)]


endef
$(error "$(error_message)")
endif

APP_LOAD_PARAMS = --delete $(COMMON_LOAD_PARAMS) --path $(APPPATH) --path "44'/1'" --tlvraw 9F:01
DEFINES += HAVE_PENDING_REVIEW_SCREEN

INCLUDES_PATH += $(BOLOS_SDK)/lib_cxng/src
APP_SOURCE_FILES += $(BOLOS_SDK)/lib_cxng/src/cx_rsa.c
APP_SOURCE_FILES += $(BOLOS_SDK)/lib_cxng/src/cx_pkcs1.c
APP_SOURCE_FILES += $(BOLOS_SDK)/lib_cxng/src/cx_utils.c
APP_SOURCE_FILES += $(BOLOS_SDK)/lib_cxng/src/cx_ram.c

NANOS_STACK_SIZE := 2880

include $(CURDIR)/../deps/ledger-zxlib/makefiles/Makefile.devices

ifeq ($(TARGET_NAME),TARGET_NANOS)
CBOR_PARSER_MAX_RECURSIONS=4
else
CBOR_PARSER_MAX_RECURSIONS=16
endif

$(info TARGET_NAME  = [$(TARGET_NAME)])
$(info ICONNAME  = [$(ICONNAME)])

ifndef ICONNAME
$(error ICONNAME is not set)
endif

include $(CURDIR)/../deps/ledger-zxlib/makefiles/Makefile.platform

DEFINES += HAVE_HASH HAVE_BLAKE2 HAVE_SHA256 HAVE_SHA512
CFLAGS += -Wvla  -Wno-implicit-fallthrough

.PHONY: rust
rust:
	@echo "No rust code"
#	cd rust && CARGO_HOME="$(CURDIR)/rust/.cargo" cargo build --target thumbv6m-none-eabi --release

# Before linking, we need to be sure rust lib is there
bin/app.elf: rust

.PHONY: rust_clean
rust_clean:
	@echo "No rust code"
#	cd rust && CARGO_HOME="$(CURDIR)/rust/.cargo" cargo clean

clean: rust_clean

# load, delete and listvariants are provided to comply with Ledger requirements
include $(CURDIR)/../deps/ledger-zxlib/makefiles/Makefile.side_loading

# Import generic rules from the SDK
include $(BOLOS_SDK)/Makefile.rules

#add dependency on custom makefile filename
dep/%.d: %.c Makefile

.PHONY: listvariants
listvariants:
	@echo VARIANTS COIN ARWEAVE

.PHONY: version
version:
	@echo "v$(APPVERSION)" > app.version
