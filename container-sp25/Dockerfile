FROM ubuntu:noble-20241118.1 AS cs225_scratch_base
    # DEBIAN_FRONTEND=noninteractive
    #   Prevents interactive prompts from transitive dependency tzdata blocking build process.
    # --no-install-recommends
    #   Reduces image size by not installing unnecessary packages.
    # rm -rf /var/lib/apt/lists/*
    #   Reduces image size by cleaning up apt files.
    #   See: https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#apt-get
    # Combine apt-get update and install within one RUN instruction.
    #   Prevents caching issues.
    #   See: https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#apt-get
    RUN DEBIAN_FRONTEND=noninteractive \
        apt-get update && \
        apt-get install --yes --no-install-recommends \
            # Build Toolchain
            #   Version locked to ensure parity with autograder.
            build-essential=12.10ubuntu1 \
            ccache=4.9.1-1 \
            clang=1:18.0-59~exp2 \
            cmake=3.28.3-1build7 \
            cmake-data=3.28.3-1build7 \
            lld=1:18.0-59~exp2 \
            ninja-build=1.11.1-2 \
            # Debugging Tools (used by autograder)
            #   Version locked to ensure parity with autograder.
            valgrind=1:3.22.0-0ubuntu3 \
            # Debugging Tools
            gdb \
            lldb \
            # Miscellaneous
            ca-certificates \
            curl \
            git \
            graphviz \
            imagemagick \
            less \
            nano \
            vim \
            wget \
        && rm -rf /var/lib/apt/lists/*

FROM cs225_scratch_base AS cs225_scratch_catch2_builder
    WORKDIR /tmp/

    # Builds Catch2 from version branch into Catch2.deb
    RUN git clone --branch v3.8.0 --depth 1 --single-branch https://github.com/catchorg/Catch2.git && \
        cd Catch2 && \
        CC=/usr/bin/clang CXX=/usr/bin/clang++ cmake -B build -S . -DBUILD_TESTING=OFF && \
        cmake --build build/ && \
        cd build/ && \
        cpack -G DEB -D CPACK_PACKAGE_FILE_NAME="Catch2"

FROM cs225_scratch_base AS cs225_base
    WORKDIR /tmp/

    # Install Catch2
    COPY --from=cs225_scratch_catch2_builder /tmp/Catch2/build/Catch2.deb Catch2.deb
    RUN dpkg -i Catch2.deb && \
        rm -rf Catch2.deb

    # Reset WORKDIR
    WORKDIR /root/

    ENV CS225_CONTAINER="sp25"