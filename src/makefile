#CXXFLAGS=-c -Wall -std=c++11 -g -mfpmath=sse -O4 -march=corei7 -pedantic
CXXFLAGS=  -fopenmp -Wall -W -std=c++11 -msse -mfpmath=sse -ffast-math -march=corei7-avx -Wa,-q -pedantic ${USERCFLAGS}
LDFLAGS= -fopenmp ${USERLDFLAGS}
SOURCES= testcase.cpp testcase_iterator.cpp main.cpp diagonals.cpp 
OBJECTS=$(SOURCES:.cpp=.o)
EXECUTABLE=../pairhmm
LIBS= 
GPU_COMPUTE_ARCH=$(subst sm,compute,$(GPU_SM_ARCH))

ifeq ($(GPU_SM_ARCH),)
error1:
	@echo "Must specify GPU architecture as sm_xx"
endif

ifeq ($(GPU),1)
  OBJECTS += compute_gpu.o
  NVCC = $(CUDA_PATH)/bin/nvcc 
  LIBS += -L$(CUDA_PATH)/lib64 -lcudart
  
  NVFLAGS = -arch=$(GPU_SM_ARCH) -gencode=arch=$(GPU_COMPUTE_ARCH),code=$(GPU_SM_ARCH) ${USERNVFLAGS}
  #LDFLAGS += -g
endif
ifeq ($(DEBUG),1)
  CXXFLAGS += -g -O0
#  NVFLAGS += -g -G -lineinfo -Xptxas -v,-abi=no
  NVFLAGS += -g -G -lineinfo
#  NVFLAGS += -g 
else
  CXXFLAGS += -O4
endif

all: $(SOURCES) $(EXECUTABLE)

$(EXECUTABLE): $(OBJECTS)
	$(CXX) $(LDFLAGS) $(OBJECTS) -o $@ $(LIBS)

diagonals.o: diagonals.cpp diagonals.h
main.o: main.cpp input_reader.h testcase_iterator.h testcase.h read.h \
 utils.h haplotype.h pairhmm.h pairhmm_impl.h constants.h diagonals.h  pairhmm_scalarimpl.h pairhmm_vecimpl.h pairhmm_sseimpl.h pairhmm_avximpl.h pairhmm_cudaimpl.h
testcase.o: testcase.cpp testcase.h read.h utils.h haplotype.h
testcase_iterator.o: testcase_iterator.cpp testcase_iterator.h testcase.h \
 read.h utils.h haplotype.h
compute_gpu.o: compute_gpu.cu compute_gpu.h
	$(NVCC) -c -o compute_gpu.o compute_gpu.cu $(NVFLAGS)

.cpp.o:
	$(CXX) $(CXXFLAGS) -c $< -o $@ 

clean:
	rm -f *.o ../pairhmm
