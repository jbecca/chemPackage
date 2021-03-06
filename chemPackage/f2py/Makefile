#
# =============================================================================
# Let's start with the declarations
# =============================================================================

# =============================================================================
# The compiler(s)
F90 = ifort
F2PY = f2py
# =============================================================================
# =============================================================================
# Flags
F90FLAGS += -FR -fPIC -heap-arrays
F90OPT += -ipo -O3 -unroll -inline
F90ARCH += -m64
# -ipo      : Interprocedural optimization - for code with more than one file
# -O3       : Optimize for maximum speed, may take longer to compile
# -unroll   : Unroll loops - this removes some overhead associated with looping
# -inline   : Places functions inside main programs
# -FR       : Free format
F2PYFLAGS += --fcompiler=intelem --f90flags='${F90FLAGS}' --arch='${F90ARCH}' \
             --opt='${F90OPT}'
# --fcompiler=intelem : Use intel's compiler
# =============================================================================
# Libraries
#MKL = -L/usr/global/intel/Compiler/2011.11/339/mkl/lib/intel64 -I/usr/global/intel/Compiler/2011.11/339/mkl/include \
      -lmkl_intel_lp64 -lmkl_intel_thread -lmkl_core -liomp5 -lpthread -lm
#      -liomp5 -lmkl_core -lmkl_intel_thread -lmkl_intel_lp64 \
#      -lmkl_def
MKL = -L$(MKLROOT)/lib/intel64 -lmkl_rt -lpthread -lm
INC += -I$(CURDIR) -I$(MKLROOT)/include
# =============================================================================

# =============================================================================
# Building rules
# =============================================================================

# "make" builds all
all: coordinate_functions tests drawingMath

coordinate_functions: coordinate_functions.so

coordinate_functions.so: calc_bonds.f90 calc_dist.f90 minmax_dist.f90 \
                         translate.f90 rotate.f90 constants.mod
	${F2PY} ${F2PYFLAGS} ${INC} ${MKL} -m coordinate_functions \
            -c calc_bonds.f90 calc_dist.f90 \
               minmax_dist.f90 translate.f90 rotate.f90

tests: tests.so

tests.so: find_equal.f90 constants.mod
	${F2PY} ${F2PYFLAGS} ${INC} ${MKL} -m tests -c find_equal.f90

drawingMath: drawingMath.so

drawingMath.so: drawingMath.f90 constants.mod
	${F2PY} ${F2PYFLAGS} ${INC} ${MKL} -m drawingMath -c drawingMath.f90

constants.mod: Constants.f90
	${F90} ${F90FLAGS} ${INC} ${MKL} -c Constants.f90



# =============================================================================
# Special rules
# =============================================================================

#  Make sure we don't remove this by accident if interrupted at the wrong time.
.PRECIOUS: Makefile

# Utility targets
.PHONY: sources install clean cleanall

# Prints to screen the source files that this program depends on
sources:
	@echo ${SRC}

# Removes objects
clean:
	@rm -vf *.o *.mod

# Removes executable
cleanall: clean
	@rm -vf *.so
