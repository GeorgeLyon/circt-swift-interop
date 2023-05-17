FROM --platform=$BUILDPLATFORM swiftlang/swift:nightly-main-jammy

RUN DEBIAN_FRONTEND=noninteractive apt-get update

ENV LC_ALL C

# Build Dependencies
RUN \
  DEBIAN_FRONTEND=noninteractive apt-get install -y \
    clang \
    clang-format \
    cmake \
    git \
    libncurses5-dev \
    ninja-build \
    python3 \
    --fix-missing 

# Install Verilator Dependencies
RUN \
  DEBIAN_FRONTEND=noninteractive apt-get install -y \
    git perl python3 make autoconf clang flex bison ccache \
    libgoogle-perftools-dev numactl perl-doc \
    libfl2 \
    libfl-dev \
    zlib1g zlib1g-dev
RUN \
  git clone -b v5.004 https://github.com/verilator/verilator /verilator-source && \
  cd /verilator-source && \
  autoconf && \
  ./configure && \
  make -j `nproc` && \
  make install

# Verilator calls `g++` even if you explicitly specify to use `clang++`. In order to avoid installing `g++` we can simply create a symbolic link
RUN cd /usr/bin && ln -s clang++ g++
