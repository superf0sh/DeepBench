
NVCC?=nvcc
ARCH?=sm_52

CUDA_INCLUDE_PATH?=/usr/include
CUDA_LIB_PATH?=/usr/lib/x86_64-linux-gnu
CUDA_PATH?=/usr/local/cuda
CUDNN_PATH?=/usr/local/cudnn
NCCL_PATH?=/usr/local/nccl
MPI_PATH?=/usr/lib/x86_64-linux-gnu/openmpi
BIN_DIR?=bin
MKDIR=mkdir -p
#BLAS
BLAS_LIBRARY?=cublas
BLAS_PATH?=$(CUDA_LIB_PATH)
#CONV
CONV_LIBRARY?=cudnn
CONV_PATH?=$
KERNELS_DIR=../kernels/
PAD_KERNELS?=1
USE_TENSOR_CORES?=0
COMMA=,
NVCC_ARCH_ARGS=$(foreach a,$(subst $(COMMA), ,$(ARCH)),--generate-code arch=$(patsubst sm_%,compute_%,$(a)),code=$(a))

.PHONY=all gemm conv rnn all_reduce nccl_single nccl_mpi clean

all: gemm conv rnn all_reduce

gemm:
	$(MKDIR) $(BIN_DIR) 
	$(NVCC) gemm_bench.cu -DUSE_TENSOR_CORES=$(USE_TENSOR_CORES) -DPAD_KERNELS=$(PAD_KERNELS) -o $(BIN_DIR)/gemm_bench -I $(KERNELS_DIR) -I $(CUDA_INCLUDE_PATH) -L $(BLAS_PATH) -l$(BLAS_LIBRARY) -L $(CUDA_LIB_PATH) -lcurand $(NVCC_ARCH_ARGS) -std=c++11

conv:
	$(MKDIR) $(BIN_DIR)
	$(NVCC) conv_bench.cu -DUSE_TENSOR_CORES=$(USE_TENSOR_CORES) -DPAD_KERNELS=$(PAD_KERNELS) -o $(BIN_DIR)/conv_bench -I $(KERNELS_DIR) -I $(CUDA_INCLUDE_PATH) -I $(CUDNN_PATH)/include/ -L $(CUDNN_PATH)/lib64/ -L $(CUDA_LIB_PATH) -lcurand -lcudnn $(NVCC_ARCH_ARGS) -std=c++11

rnn:
	$(MKDIR) $(BIN_DIR)
	$(NVCC) rnn_bench.cu -DUSE_TENSOR_CORES=$(USE_TENSOR_CORES) -o $(BIN_DIR)/rnn_bench -I $(KERNELS_DIR) -I $(CUDA_INCLUDE_PATH) -I $(CUDNN_PATH)/include/ -L $(CUDNN_PATH)/lib64/ -L $(CUDA_LIB_PATH) -lcurand -lcudnn $(NVCC_ARCH_ARGS) -std=c++11

all_reduce: nccl_single nccl_mpi

nccl_single:
	$(MKDIR) $(BIN_DIR)
	$(NVCC) nccl_single_all_reduce.cu -o $(BIN_DIR)/nccl_single_all_reduce -I $(KERNELS_DIR) -I $(NCCL_PATH)/include/ -I $(CUDNN_PATH)/include/ -L $(NCCL_PATH)/lib/ -L $(CUDNN_PATH)/lib64 -lnccl -lcudart -lcurand $(NVCC_ARCH_ARGS) -std=c++11 

nccl_mpi:
	$(MKDIR) $(BIN_DIR)
	$(NVCC) nccl_mpi_all_reduce.cu -o $(BIN_DIR)/nccl_mpi_all_reduce -I $(KERNELS_DIR) -I $(NCCL_PATH)/include/ -I $(CUDNN_PATH)/include/ -I $(MPI_PATH)/include -L $(NCCL_PATH)/lib/ -L $(CUDNN_PATH)/lib64 -L $(MPI_PATH)/lib -lnccl -lcurand -lcudart -lmpi $(NVCC_ARCH_ARGS) -std=c++11 

sparse:
	$(MKDIR) $(BIN_DIR) 
	$(NVCC) sparse_bench.cu -o $(BIN_DIR)/sparse_bench -I $(KERNELS_DIR) -I $(CUDA_INCLUDE_PATH) -L $(BLAS_PATH) -l$(BLAS_LIBRARY) -L $(CUDA_LIB_PATH) -lcurand -lcusparse $(NVCC_ARCH_ARGS) -std=c++11


clean:
	rm -rf $(BIN_DIR)

rebuild: clean all
