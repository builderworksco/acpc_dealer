THIS_DIR := $(CURDIR)/$(dir $(lastword $(MAKEFILE_LIST)))
PROJECT_DIR := $(THIS_DIR)/../

CC = gcc -std=gnu99
CFLAGS = -O3 -Wall
LIBS = -lm
TARGET_SYMBOLS = -DCEXCEPTION_USE_CONFIG_FILE

VENDOR_DIR := $(THIS_DIR)/vendor/
COMMANDER_DIR := $(VENDOR_DIR)/commander.c
TARGET_INCLUDES = -I$(PROJECT_DIR) -I$(THIS_DIR)/src -I$(CEXCEPTION_LIB) -I$(COMMANDER_DIR)/src

CEXCEPTION_DIR := $(VENDOR_DIR)/cexception/
CEXCEPTION_LIB := $(CEXCEPTION_DIR)/lib
CEXCEPTION_SRC := $(CEXCEPTION_LIB)/CException.c

TARGET_BASE = kuhn_3p_equilibrium_player
TARGET_SRC := $(THIS_DIR)/src/$(TARGET_BASE).c $(CEXCEPTION_SRC)

PROJECT_SRC := $(PROJECT_DIR)/game.c $(PROJECT_DIR)/rng.c $(PROJECT_DIR)/net.c

BUILD_PREFIX := $(CC) $(CFLAGS) $(TARGET_SYMBOLS)

all: clean test

# Build object
#==============
TARGET := $(THIS_DIR)/$(TARGET_BASE)

$(TARGET): $(TARGET_SRC) $(PROJECT_DIR)/evalHandTables $(PROJECT_DIR)/game.h $(PROJECT_DIR)/net.h $(PROJECT_DIR)/rng.h
	$(BUILD_PREFIX) $(TARGET_INCLUDES) $(TARGET_SRC) $(THIS_DIR)/src/main.c $(COMMANDER_DIR)/src/commander.c $(LIBS) -o $@ $(PROJECT_SRC)
	chmod 755 $@

# Testing
#===========
UNITY_DIR := $(VENDOR_DIR)/unity
CMOCK_DIR := $(VENDOR_DIR)/cmock
TEST_DIR := $(THIS_DIR)/test
TEST_SUPPORT_DIR := $(TEST_DIR)/support
INC_DIRS := -I$(UNITY_DIR)/src -I$(TEST_SUPPORT_DIR) -I$(THIS_DIR)/src $(TARGET_INCLUDES)
SYMBOLS = -DTEST -DUNITY_FLOAT_TYPE=double -DUNITY_FLOAT_VERBOSE
TEST_BASE := $(TEST_DIR)/test_$(TARGET_BASE)
ifeq ($(OS),Windows_NT)
	TEST_EXTENSION=.exe
else
	TEST_EXTENSION=.out
endif

# Test 1
#-------------
TEST1_BASE := $(TEST_BASE)
TEST1_SRC := $(TEST1_BASE).c
TEST1 := $(TEST1_BASE)$(TEST_EXTENSION)
TEST1_RUNNER := $(TEST1_BASE)_Runner.c

$(TEST1_RUNNER):
	ruby $(UNITY_DIR)/auto/generate_test_runner.rb $(TEST1_SRC) $(TEST1_RUNNER)

$(TEST1): $(TARGET) $(TEST1_RUNNER)
	$(BUILD_PREFIX) $(SYMBOLS) $(INC_DIRS) $(TARGET_SRC) $(PROJECT_SRC) $(TEST_SUPPORT_DIR)/test_helper.c $(UNITY_DIR)/src/unity.c $(TEST1_SRC) $(TEST1_RUNNER) $(LIBS) -o $(TEST1)

# Test 2
#-------------
TEST2_BASE := $(TEST_BASE)_sub_family_1
TEST2_SRC := $(TEST2_BASE).c
TEST2 := $(TEST2_BASE)$(TEST_EXTENSION)
TEST2_RUNNER := $(TEST2_BASE)_Runner.c

$(TEST2_RUNNER):
	ruby $(UNITY_DIR)/auto/generate_test_runner.rb $(TEST2_SRC) $(TEST2_RUNNER)

$(TEST2): $(TARGET) $(TEST2_RUNNER)
	$(BUILD_PREFIX) $(SYMBOLS) $(INC_DIRS) $(TARGET_SRC) $(PROJECT_SRC) $(TEST_SUPPORT_DIR)/test_helper.c $(UNITY_DIR)/src/unity.c $(TEST2_SRC) $(TEST2_RUNNER) $(LIBS) -o $(TEST2)

# Test 3
#-------------
TEST3_BASE := $(TEST_BASE)_sub_family_2
TEST3_SRC := $(TEST3_BASE).c
TEST3 := $(TEST3_BASE)$(TEST_EXTENSION)
TEST3_RUNNER := $(TEST3_BASE)_Runner.c

$(TEST3_RUNNER):
	ruby $(UNITY_DIR)/auto/generate_test_runner.rb $(TEST3_SRC) $(TEST3_RUNNER)

$(TEST3): $(TARGET) $(TEST3_RUNNER)
	$(BUILD_PREFIX) $(SYMBOLS) $(INC_DIRS) $(TARGET_SRC) $(PROJECT_SRC) $(TEST_SUPPORT_DIR)/test_helper.c $(UNITY_DIR)/src/unity.c $(TEST3_SRC) $(TEST3_RUNNER) $(LIBS) -o $(TEST3)


# Test
#-----------------
test: $(TEST1) $(TEST2) $(TEST3)
	$(TEST1)
	$(TEST2)
	$(TEST3)


# Clean
#-----------------------------------
clean:
	rm -f $(TARGET) *.o *.il *.out $(TEST1) $(TEST2) $(TEST3) $(TEST1_RUNNER) $(TEST2_RUNNER) $(TEST3_RUNNER) $(THIS_DIR)/test/*.out

dist_clean: clean
	rm -f *~ core