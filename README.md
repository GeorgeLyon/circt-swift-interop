# Example of using C++ interop with CIRCT (MLIR)

## Structure

The [Test Target](circt/test/Bindings/Swift) depends on [the Swift API Target](circt/lib/Bindings/Swift/CIRCTSwift)implemented using C++ APIs imported from [a C++ Target](circt/lib/Bindings/Swift/Bridge/) which imports [MLIR using a modulemap](circt/llvm/mlir/include/mlir/module.modulemap).

Compile options for both the Swift API and the Test Target are the following:
```
target_compile_options(Target
  PRIVATE
    -emit-module
    "SHELL:-cxx-interoperability-mode=swift-5.9"
    "SHELL:-Xfrontend -enable-experimental-move-only"
    "SHELL:-Xfrontend -enable-experimental-feature -Xfrontend MoveOnly"
    "SHELL:-Xfrontend -enable-experimental-feature -Xfrontend MoveOnlyClass"
    "SHELL:-Xfrontend -enable-experimental-feature -Xfrontend Macros"
    "SHELL:-Xfrontend -validate-tbd-against-ir=none"
    -Xcc -std=c++17)
```

## Current Status

We are attempting to use [`MLIRContext`](circt/llvm/mlir/include/mlir/IR/MLIRContext.h) which has a deleted copy constructer and assignment operator.

The error we get back is:
```
[1/2] Linking Swift static library lib/libCIRCTSwift.a
FAILED: lib/libCIRCTSwift.a tools/circt/lib/Bindings/Swift/CIRCTSwift/CMakeFiles/CIRCTSwift.dir/MLIRContext.swift.o swift/CIRCTSwift.swiftmodule 
: && /usr/bin/swiftc -output-file-map tools/circt/lib/Bindings/Swift/CIRCTSwift/CMakeFiles/CIRCTSwift.dir/Debug/output-file-map.json -incremental -j 10 -emit-library -static -o lib/libCIRCTSwift.a -module-name CIRCTSwift -module-link-name CIRCTSwift -emit-module -emit-module-path swift/CIRCTSwift.swiftmodule -emit-dependencies -D_DEBUG -D_GLIBCXX_ASSERTIONS -D_GNU_SOURCE -D_LIBCPP_ENABLE_ASSERTIONS -D__STDC_CONSTANT_MACROS -D__STDC_FORMAT_MACROS -D__STDC_LIMIT_MACROS -g -emit-module -cxx-interoperability-mode=swift-5.9 -Xfrontend -enable-experimental-move-only -Xfrontend -enable-experimental-feature -Xfrontend MoveOnly -Xfrontend -enable-experimental-feature -Xfrontend MoveOnlyClass -Xfrontend -enable-experimental-feature -Xfrontend Macros -Xfrontend -validate-tbd-against-ir=none -Xcc -std=c++17 -I /workspaces/circt-swift-interop/build/tools/circt/lib/Bindings/Swift/CIRCTSwift -I /workspaces/circt-swift-interop/circt/lib/Bindings/Swift/CIRCTSwift -I /workspaces/circt-swift-interop/build/include -I /workspaces/circt-swift-interop/circt/llvm/llvm/include -I /workspaces/circt-swift-interop/circt/include -I /workspaces/circt-swift-interop/build/tools/circt/include -I /workspaces/circt-swift-interop/circt/llvm/llvm/../mlir/include -I /workspaces/circt-swift-interop/build/tools/mlir/include -I /usr/include /workspaces/circt-swift-interop/circt/lib/Bindings/Swift/CIRCTSwift/MLIRContext.swift    && :
/workspaces/circt-swift-interop/circt/lib/Bindings/Swift/CIRCTSwift/MLIRContext.swift:5:16: error: 'MLIRContext' is not a member type of enum '__ObjC.mlir'
extension mlir.MLIRContext {
          ~~~~ ^
__ObjC.mlir:1:13: note: 'mlir' declared here
public enum mlir {
            ^
error: emit-module command failed with exit code 1 (use -v to see invocation)
/workspaces/circt-swift-interop/circt/lib/Bindings/Swift/CIRCTSwift/MLIRContext.swift:5:16: error: 'MLIRContext' is not a member type of enum '__ObjC.mlir'
extension mlir.MLIRContext {
          ~~~~ ^
__ObjC.mlir:1:13: note: 'mlir' declared here
public enum mlir {
            ^
error: fatalError
ninja: build stopped: subcommand failed.
```

## Building
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
cmake \
  --build build \
  --config Debug \
  --target circt-swift-bindings-test \
  --
build/bin/circt-swift-bindings-test
```