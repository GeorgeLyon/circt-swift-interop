# Example of using C++ interop with CIRCT (MLIR)

## Configure
```
cmake \
  -DCMAKE_C_COMPILER=clang \
  -DCMAKE_CXX_COMPILER=clang++ \
  -DLLVM_ENABLE_ASSERTIONS=ON \
  -DLLVM_ENABLE_PROJECTS=mlir \
  -DLLVM_TARGETS_TO_BUILD=host \
  -DLLVM_BUILD_EXAMPLES=OFF \
  -DLLVM_ENABLE_OCAMLDOC=OFF \
  -DLLVM_ENABLE_BINDINGS=OFF \
  -DLLVM_ENABLE_MODULES=OFF \
  -DLLVM_OPTIMIZED_TABLEGEN=ON \
  -DLLVM_USE_SPLIT_DWARF=ON \
  -DLLVM_CCACHE_BUILD=OFF \
  -DLLVM_EXTERNAL_PROJECTS=circt \
  -DLLVM_EXTERNAL_CIRCT_SOURCE_DIR=circt \
  -DCIRCT_BINDINGS_SWIFT_ENABLED=ON \
  -DCMAKE_BUILD_TYPE=Debug \
  -S circt/llvm/llvm \
  -B build \
  -G Ninja
```
## Build
```
cmake \
  --build build \
  --config Debug \
  --target circt-swift-bindings-test \
  --
```
## Run
```
build/bin/circt-swift-bindings-test
```