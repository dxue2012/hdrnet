TF_INC ?= `python -c 'import tensorflow as tf; print(tf.sysconfig.get_include())'`
# TF_INC ?= /usr/local/lib/python2.7/dist-packages/tensorflow/include
CUDA_HOME ?= /usr/local/cuda

SRC_DIR = ops

BUILD_DIR = build
LIB_DIR = lib

CC = c++ -std=c++11 #-g
NVCC = nvcc -std c++11 #-g -pg
# Use flag -D _GLIBCXX_USE_CXX11_ABI=0 for gcc > 5
# Use flag -undefined dynamic_lookup for OSX
CFLAGS = -fPIC -I$(TF_INC) -O2 -undefined dynamic_lookup
LDFLAGS = -L$(CUDA_HOME)/lib -lcudart
# Use flag -D _GLIBCXX_USE_CXX11_ABI=0 for gcc > 5
NVFLAGS = -DGOOGLE_CUDA=1 -x cu -Xcompiler -fPIC -I $(TF_INC) \
					-expt-relaxed-constexpr -Wno-deprecated-gpu-targets -ftz=true


SRC = bilateral_slice.cc
CUDA_SRC = bilateral_slice.cu.cc
CUDA_OBJ = $(addprefix $(BUILD_DIR)/,$(CUDA_SRC:.cc=.o))
SRCS = $(addprefix $(SRC_DIR)/, $(SRC))

# Make sure to update PATH and LD_LIBRARY_PATH for nvcc to be found
# export PATH=/usr/local/cuda-8.0/bin${PATH:+:${PATH}}
# export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64\${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
all: $(LIB_DIR)/hdrnet_ops.so

# Main library
$(LIB_DIR)/hdrnet_ops.so: $(CUDA_OBJ) $(LIB_DIR) $(SRCS)
	$(CC) -shared -o $@ $(SRCS) $(CUDA_OBJ) $(CFLAGS) $(LDFLAGS)

# Cuda kernels
$(BUILD_DIR)/%.o: $(SRC_DIR)/%.cc $(BUILD_DIR)
	$(NVCC) -c  $< -o $@ $(NVFLAGS)

$(BUILD_DIR):
	mkdir -p $@

$(LIB_DIR):
	mkdir -p $@

clean:
	rm -rf $(BUILD_DIR) $(LIB_DIR)
