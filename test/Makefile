# [based on template from Google Test]

# Please tweak the following variable definitions as needed by your
# project, except GMOCK_HEADERS and GTEST_HEADERS, which you can use
# in your own targets but shouldn't modify.

# Points to the root of Google Test, relative to where this file is.
# Remember to tweak this if you move this file, or if you want to use
# a copy of Google Test at a different location.
WORKING_DIR = $(shell pwd)
GTEST_DIR = $(WORKING_DIR)/gtest/googletest

# Points to the root of Google Mock, relative to where this file is.
# Remember to tweak this if you move this file.
GMOCK_DIR = $(WORKING_DIR)/gtest/googlemock

# Location of main (non-unittest) code
MAIN_DIR = ..

# Flags passed to the preprocessor.
# Set Google Test and Google Mock's header directories as system
# directories, such that the compiler doesn't generate warnings in
# these headers.
CPPFLAGS += -isystem $(GTEST_DIR)/include -isystem $(GMOCK_DIR)/include

# Flags passed to the C++ compiler.
CXXFLAGS += -g -Wall -Wextra -pthread -std=c++14

# All tests produced by this Makefile.  Remember to add new tests you
# created to the list.
TESTS = test

# All Google Test headers.  Usually you shouldn't change this
# definition.
GTEST_HEADERS = $(GTEST_DIR)/include/gtest/*.h \
                $(GTEST_DIR)/include/gtest/internal/*.h

# All Google Mock headers. Note that all Google Test headers are
# included here too, as they are #included by Google Mock headers.
# Usually you shouldn't change this definition.	
GMOCK_HEADERS = $(GMOCK_DIR)/include/gmock/*.h \
                $(GMOCK_DIR)/include/gmock/internal/*.h \
                $(GTEST_HEADERS)

# House-keeping build targets.

all: $(TESTS) blink test-tcpserver

clean-gtest:
	rm -f gmock.a gmock_main.a

clean:
	rm -f $(TESTS) *.o blink test-tcpserver

# Builds gmock.a and gmock_main.a.  These libraries contain both
# Google Mock and Google Test.  A test should link with either gmock.a
# or gmock_main.a, depending on whether it defines its own main()
# function.  It's fine if your test only uses features from Google
# Test (and not Google Mock).

# Usually you shouldn't tweak such internal variables, indicated by a
# trailing _.
GTEST_SRCS_ = $(GTEST_DIR)/src/*.cc $(GTEST_DIR)/src/*.h $(GTEST_HEADERS)
GMOCK_SRCS_ = $(GMOCK_DIR)/src/*.cc $(GMOCK_HEADERS)

# For simplicity and to avoid depending on implementation details of
# Google Mock and Google Test, the dependencies specified below are
# conservative and not optimized.  This is fine as Google Mock and
# Google Test compile fast and for ordinary users their source rarely
# changes.
gtest-all.o: $(GTEST_SRCS_)
	$(CXX) $(CPPFLAGS) -I$(GTEST_DIR) -I$(GMOCK_DIR) $(CXXFLAGS) \
            -c $(GTEST_DIR)/src/gtest-all.cc

gmock-all.o: $(GMOCK_SRCS_)
	$(CXX) $(CPPFLAGS) -I$(GTEST_DIR) -I$(GMOCK_DIR) $(CXXFLAGS) \
            -c $(GMOCK_DIR)/src/gmock-all.cc

gmock_main.o: $(GMOCK_SRCS_)
	$(CXX) $(CPPFLAGS) -I$(GTEST_DIR) -I$(GMOCK_DIR) $(CXXFLAGS) \
            -c $(GMOCK_DIR)/src/gmock_main.cc

gmock.a: gmock-all.o gtest-all.o
	$(AR) $(ARFLAGS) $@ $^

gmock_main.a: gmock-all.o gtest-all.o gmock_main.o
	$(AR) $(ARFLAGS) $@ $^

test.o: test.cpp
	$(CXX) $(CPPFLAGS) $(CXXFLAGS) -I$(MAIN_DIR) -c $<

test: test.o $(MAIN_DIR)/common.o $(MAIN_DIR)/pwm.o $(MAIN_DIR)/network.o $(MAIN_DIR)/filesystem.o gmock_main.a
	$(CXX) $(CPPFLAGS) $(CXXFLAGS) -lpthread $^ -o $@

########################################################################
# Other (non-unit) tests

test-tcpserver.o: test-tcpserver.cpp
	$(CXX) $(CPPFLAGS) $(CXXFLAGS) -I$(MAIN_DIR) -c $<

blink.o: blink.cpp
	$(CXX) $(CPPFLAGS) $(CXXFLAGS) -I$(MAIN_DIR) -c $<

test-tcpserver: test-tcpserver.o $(MAIN_DIR)/common.o $(MAIN_DIR)/network.o
	$(CXX) $(CPPFLAGS) $(CXXFLAGS) -lpthread $^ -o $@

blink: $(MAIN_DIR)/common.o $(MAIN_DIR)/pwm.o blink.o
	$(CXX) $(CPPFLAGS) $(CXXFLAGS) $^ -o $@
