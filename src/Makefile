################################################################################
# Makefile for Train Door Control System - SIL 3
# EN 50128:2011 Compliant Build System
#
# Project: train_door_control2
# Safety Integrity Level: SIL 3
# Standard: EN 50128:2011, MISRA C:2012
#
# Build targets:
#   all         - Build all object files and library
#   clean       - Remove all build artifacts
#   check       - Run static analysis (requires cppcheck)
#   complexity  - Analyze cyclomatic complexity (requires lizard)
#   help        - Display this help message
#
################################################################################

# Compiler and tools
CC := gcc
AR := ar
CPPCHECK := cppcheck
LIZARD := lizard

# Project directories
SRC_DIR := .
BUILD_DIR := build
OBJ_DIR := $(BUILD_DIR)/obj

# Compiler flags (EN 50128 SIL 3 requirements)
CFLAGS := -std=c99 \
          -pedantic \
          -Wall \
          -Wextra \
          -Werror \
          -Wconversion \
          -Wsign-conversion \
          -Wcast-qual \
          -Wcast-align \
          -Wpointer-arith \
          -Wstrict-prototypes \
          -Wmissing-prototypes \
          -Wunused \
          -Wuninitialized \
          -Wshadow \
          -O2 \
          -fno-common \
          -ffunction-sections \
          -fdata-sections

# Include directories
INCLUDES := -I$(SRC_DIR)/common \
            -I$(SRC_DIR)/door_control \
            -I$(SRC_DIR)/safety_monitor \
            -I$(SRC_DIR)/fault_detection \
            -I$(SRC_DIR)/command_processor \
            -I$(SRC_DIR)/status_reporter \
            -I$(SRC_DIR)/actuator_hal \
            -I$(SRC_DIR)/sensor_hal \
            -I$(SRC_DIR)/communication_hal \
            -I$(SRC_DIR)/hal_drivers

# Source files
COMMON_SRC := $(SRC_DIR)/common/types.c

MODULE_SRC := $(SRC_DIR)/door_control/door_fsm.c \
              $(SRC_DIR)/safety_monitor/safety_monitor.c \
              $(SRC_DIR)/fault_detection/fault_detection.c \
              $(SRC_DIR)/command_processor/command_processor.c \
              $(SRC_DIR)/status_reporter/status_reporter.c \
              $(SRC_DIR)/actuator_hal/actuator_hal.c \
              $(SRC_DIR)/sensor_hal/sensor_hal.c \
              $(SRC_DIR)/communication_hal/communication_hal.c

HAL_STUBS_SRC := $(SRC_DIR)/hal_drivers/hal_driver_stubs.c

ALL_SRC := $(COMMON_SRC) $(MODULE_SRC) $(HAL_STUBS_SRC)

# Object files
COMMON_OBJ := $(patsubst $(SRC_DIR)/%.c,$(OBJ_DIR)/%.o,$(COMMON_SRC))
MODULE_OBJ := $(patsubst $(SRC_DIR)/%.c,$(OBJ_DIR)/%.o,$(MODULE_SRC))
HAL_STUBS_OBJ := $(patsubst $(SRC_DIR)/%.c,$(OBJ_DIR)/%.o,$(HAL_STUBS_SRC))
ALL_OBJ := $(COMMON_OBJ) $(MODULE_OBJ) $(HAL_STUBS_OBJ)

# Output library
LIB_NAME := libtrain_door_control.a
LIB_PATH := $(BUILD_DIR)/$(LIB_NAME)

################################################################################
# Build Targets
################################################################################

.PHONY: all clean check complexity help dirs

# Default target
all: dirs $(LIB_PATH)
	@echo ""
	@echo "=========================================="
	@echo "Build complete!"
	@echo "=========================================="
	@echo "Library: $(LIB_PATH)"
	@echo "Object files: $(words $(ALL_OBJ))"
	@echo ""
	@echo "Next steps:"
	@echo "  make check      - Run static analysis"
	@echo "  make complexity - Check cyclomatic complexity"
	@echo ""

# Create output directories
dirs:
	@mkdir -p $(BUILD_DIR)
	@mkdir -p $(OBJ_DIR)/common
	@mkdir -p $(OBJ_DIR)/door_control
	@mkdir -p $(OBJ_DIR)/safety_monitor
	@mkdir -p $(OBJ_DIR)/fault_detection
	@mkdir -p $(OBJ_DIR)/command_processor
	@mkdir -p $(OBJ_DIR)/status_reporter
	@mkdir -p $(OBJ_DIR)/actuator_hal
	@mkdir -p $(OBJ_DIR)/sensor_hal
	@mkdir -p $(OBJ_DIR)/communication_hal
	@mkdir -p $(OBJ_DIR)/hal_drivers

# Link static library
$(LIB_PATH): $(ALL_OBJ)
	@echo "Creating static library: $@"
	$(AR) rcs $@ $^

# Compile C source files
$(OBJ_DIR)/%.o: $(SRC_DIR)/%.c
	@echo "Compiling: $<"
	$(CC) $(CFLAGS) $(INCLUDES) -c $< -o $@

################################################################################
# Analysis Targets
################################################################################

# Static analysis with cppcheck
check:
	@echo "=========================================="
	@echo "Running static analysis (cppcheck)..."
	@echo "=========================================="
	@if command -v $(CPPCHECK) >/dev/null 2>&1; then \
		$(CPPCHECK) --enable=all \
		            --std=c99 \
		            --platform=unix32 \
		            --suppress=missingIncludeSystem \
		            --suppress=unusedFunction \
		            --inline-suppr \
		            --error-exitcode=1 \
		            $(INCLUDES) \
		            $(COMMON_SRC) $(MODULE_SRC); \
	else \
		echo "WARNING: cppcheck not found. Please install cppcheck for static analysis."; \
		echo "  Debian/Ubuntu: sudo apt-get install cppcheck"; \
		echo "  macOS: brew install cppcheck"; \
	fi

# Cyclomatic complexity analysis
complexity:
	@echo "=========================================="
	@echo "Analyzing cyclomatic complexity (lizard)..."
	@echo "=========================================="
	@if command -v $(LIZARD) >/dev/null 2>&1; then \
		$(LIZARD) -l c \
		          --CCN 10 \
		          --length 150 \
		          --arguments 5 \
		          --warnings_only \
		          $(COMMON_SRC) $(MODULE_SRC); \
	else \
		echo "WARNING: lizard not found. Please install lizard for complexity analysis."; \
		echo "  pip install lizard"; \
	fi

# Clean build artifacts
clean:
	@echo "Cleaning build artifacts..."
	rm -rf $(BUILD_DIR)
	@echo "Clean complete."

# Help target
help:
	@echo "=========================================="
	@echo "Train Door Control System - Build System"
	@echo "=========================================="
	@echo ""
	@echo "Targets:"
	@echo "  all         - Build all object files and library (default)"
	@echo "  clean       - Remove all build artifacts"
	@echo "  check       - Run static analysis with cppcheck"
	@echo "  complexity  - Analyze cyclomatic complexity with lizard"
	@echo "  help        - Display this help message"
	@echo ""
	@echo "Build Configuration:"
	@echo "  Compiler: $(CC)"
	@echo "  Standard: C99 (ISO/IEC 9899:1999)"
	@echo "  Safety: SIL 3 (EN 50128:2011)"
	@echo "  Coding: MISRA C:2012"
	@echo ""
	@echo "Source Files:"
	@echo "  Common:      $(words $(COMMON_SRC)) files"
	@echo "  Modules:     $(words $(MODULE_SRC)) files"
	@echo "  HAL Stubs:   $(words $(HAL_STUBS_SRC)) files"
	@echo "  Total:       $(words $(ALL_SRC)) files"
	@echo ""

################################################################################
# Dependencies (auto-generated)
################################################################################

-include $(ALL_OBJ:.o=.d)

# Generate dependency files
$(OBJ_DIR)/%.d: $(SRC_DIR)/%.c
	@mkdir -p $(dir $@)
	@$(CC) $(CFLAGS) $(INCLUDES) -MM -MT $(patsubst %.d,%.o,$@) $< > $@
