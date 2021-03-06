##### TEMPLATE #####

#
# USE GCC 4.1 
# USE G++ 4.1
#

# Fill in the name of the output binary here
target    := bondsgpu

# List of sources, with .c, .cu, and .cc extensions
sources   := 	../gpu_funcs/gpu_bonds.cu \
				../gpu_funcs/gpu_bonds_kernel.cu \
				../gpu_funcs/gpu_autocorrelation.cu \
				../gpu_funcs/gpu_autocorrelation_kernel.cu \
				main.cpp \
				../natoms/xyz_display_open_gl.cpp 

# path to thrust folder
THRUST  := -I../thrust/

# path to NVIDIA CUDA SDK
NVIDIASDK := $(HOME)/NVIDIA_GPU_Computing_SDK/


# Flags common to all compilers. You can set these on the comamnd line, e.g:
# $ make opt="" dbg="-g" warn="-Wno-deptrcated-declarations -Wall -Werror"

opt  ?= -O2 -arch sm_11 # use -arch sm_12 for shared atomics
dbg  ?= 
warn ?= -Wall -Werror -fno-strict-aliasing -O2


# This is where the cuda runtime libraries and includes reside.
cudaroot  := /usr/local/cuda


#----- C compilation options ------ 
gcc        := /usr/bin/gcc
cflags     :=  $(dbg) $(warn)
clib_paths := -L$(cudaroot)/lib64 -L$(NVIDIASDK)C/common/lib/linux 
cinc_paths := -I$(cudaroot)/include -I$(NVIDIASDK)C/common/inc -I../gpu_funcs/
clibraries := -lcuda -lcudart 


#----- C++ compilation options ------
gpp         := /usr/bin/g++
ccflags     :=  $(dbg) $(warn)
cclib_paths := -L$(cudaroot)/lib64 -L$(NVIDIASDK)C/common/lib/linux 
ccinc_paths := -I$(cudaroot)/include -I$(NVIDIASDK)C/common/inc -I../gpu_funcs/
cclibraries := -lcuda -lcudart  -lm -lGL -lGLU -lglut -lpthread 

#----- CUDA compilation options -----
nvcc        := $(cudaroot)/bin/nvcc
cuflags     := $(opt) $(dbg)   
culib_paths := -L$(cudaroot)/lib64 -L$(NVIDIASDK)C/common/lib/linux 
cuinc_paths := -I$(cudaroot)/include -I$(NVIDIASDK)C/common/inc  $(THRUST) -I../gpu_funcs/
culibraries := -lcuda -lcudart 

#-----
lib_paths   := $(culib_paths) $(cclib_paths) $(clib_paths)
libraries   := $(culibraries) $(cclibraries) $(clibraries)


#----- Generate source file and object file lists
# This code separates the source files by filename extension into C, C++,
# and Cuda files.

csources  := $(filter %.c, $(sources)) 
ccsources := $(filter %.cpp, $(sources)) 
#             $(filter %.cc,$(sources)) 
cusources := $(filter %.cu, $(sources))

# This code generates a list of object files by replacing filename extensions

objects := $(patsubst %.cu, %.o,$(cusources)) \
           $(patsubst %.c,  %.o,$(csources) ) \
           $(patsubst %.cpp,%.o,$(ccsources))
#           $(patsubst %.cc, %.o,$(ccsources)) \

#----- Build rules ------

$(target): $(objects)
	$(gcc) -o $@ $(lib_paths) $(libraries) $(objects)

%.o: %.cu
	$(nvcc) -c $^ $(cuflags) $(cuinc_paths) -o $@

%.cubin: %.cu
	$(nvcc) -cubin $(cuflags) $(cuinc_paths) $^

%.o: %.cc
	$(gpp) -c $^ $(ccflags) $(ccinc_paths) -o $@

%.o: %.cpp
	$(gpp) -c $^ $(ccflags) $(ccinc_paths) -o $@	

%.o: %.c
	$(gcc) -c $^ $(cflags) $(cinc_paths) -o $@

clean:
	rm -f *.o $(target) makefile.*dep ../gpu_funcs/*.o


#----- Dependency Generation -----
#
# If a particular set of sources is non-empty, then have rules for
# generating the necessary dep files.
#

#ccdep := ccdep.mk
#cdep  := cdep.mk
#cudep := cudep.mk
ccdep := 
cdep  := 
cudep := 


depfiles = 

ifneq ($(ccsources),)

depfiles += $(ccdep)
$(ccdep): $(ccsources)
	$(gpp) -MM $(ccsources) > $(ccdep)

else

$(ccdep):

endif

ifneq ($(cusources),)

depfiles += $(cudep)
$(cudep):
	$(gpp) -MM -x c++ $(cusources) > $(cudep)

else

$(cudep):

endif

ifneq ($(csources),)

depfiles += $(cdep)
$(cdep): $(csources)
	$(gcc) -MM -x c $(csources) > $(cdep)

else

$(cdep):

endif

.PHONY: dep
dep: $(depfiles)


ifneq ($(MAKECMDGOALS),dep)
 ifneq ($(MAKECMDGOALS),clean)
  ifneq ($(ccsources),)
   include $(ccdep)
  endif
  ifneq ($(cusources),)
   include $(cudep)
  endif
  ifneq ($(csources),)
   include $(cdep)
  endif
 endif
endif
