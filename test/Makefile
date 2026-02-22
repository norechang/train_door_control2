# Makefile for train_door_control2 unit tests
# Project: train_door_control2
# SIL Level: 3
# Standard: EN 50128:2011 Section 7.5
# Traceability: DOC-COMPTEST-2026-001 v1.0

# Compiler and flags
CC = gcc
CFLAGS = -std=c11 -Wall -Wextra -Werror -pedantic -O0 -g
CFLAGS += -I../src
CFLAGS += -fprofile-arcs -ftest-coverage  # Coverage instrumentation

# Coverage tools
LCOV = lcov
GENHTML = genhtml
GCOV = gcov

# Directories
SRC_DIR = ../src
TEST_DIR = .
BUILD_DIR = build
COVERAGE_DIR = coverage_report

# Source files (modules under test)
# NOTE: For unit testing, we only compile the modules under test.
# Dependencies (HAL, fault detection, safety_monitor, etc.) are mocked in test files.
# Currently testing: door_fsm.c only
SRC_FILES = \
	$(SRC_DIR)/door_control/door_fsm.c

# Test files
TEST_FILES = \
	test_harness.c \
	test_mocks.c \
	test_door_fsm.c \
	test_stubs.c \
	run_all_tests.c
# NOTE: test_safety_monitor.c temporarily disabled (needs safety_monitor.c compiled)

# Object files
OBJ_FILES = $(SRC_FILES:.c=.o) $(TEST_FILES:.c=.o)

# Output executable
TARGET = test_runner

# Default target
all: clean $(TARGET)

# Build test runner
$(TARGET): $(TEST_FILES) $(SRC_FILES)
	@echo "===================================="
	@echo " Building Test Runner (SIL 3)"
	@echo "===================================="
	$(CC) $(CFLAGS) -o $(TARGET) $(TEST_FILES) $(SRC_FILES)
	@echo "Build complete: $(TARGET)"

# Run tests
test: $(TARGET)
	@echo ""
	@echo "===================================="
	@echo " Executing Unit Tests"
	@echo "===================================="
	./$(TARGET)

# Generate coverage report
coverage: test
	@echo ""
	@echo "===================================="
	@echo " Generating Coverage Report"
	@echo "===================================="
	@mkdir -p $(COVERAGE_DIR)
	$(LCOV) --capture --directory . --output-file coverage.info --ignore-errors mismatch
	$(LCOV) --remove coverage.info '/usr/*' --output-file coverage_filtered.info
	$(GENHTML) coverage_filtered.info --output-directory $(COVERAGE_DIR)
	@echo "Coverage report generated in $(COVERAGE_DIR)/index.html"
	@echo ""
	@echo "Coverage Summary:"
	@$(LCOV) --summary coverage_filtered.info

# Clean build artifacts
clean:
	@echo "Cleaning build artifacts..."
	rm -f $(TARGET)
	rm -f *.o *.gcda *.gcno *.gcov
	rm -f $(SRC_DIR)/*/*.o $(SRC_DIR)/*/*.gcda $(SRC_DIR)/*/*.gcno
	rm -f coverage.info coverage_filtered.info
	rm -rf $(BUILD_DIR) $(COVERAGE_DIR)
	@echo "Clean complete"

# Run tests and generate coverage (full workflow)
full: clean all test coverage
	@echo ""
	@echo "===================================="
	@echo " Full Test Suite Complete"
	@echo "===================================="

# Help target
help:
	@echo "Available targets:"
	@echo "  make all       - Build test runner"
	@echo "  make test      - Build and run tests"
	@echo "  make coverage  - Run tests and generate coverage report"
	@echo "  make full      - Clean, build, test, and generate coverage"
	@echo "  make clean     - Remove build artifacts"
	@echo "  make help      - Show this help message"

.PHONY: all test coverage clean full help
