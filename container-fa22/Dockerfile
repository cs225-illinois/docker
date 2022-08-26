FROM ubuntu:jammy-20220801 AS cs225_base_no_catch2
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
            build-essential=12.9ubuntu3 \
            clang=1:14.0-55~exp2 \
            cmake=3.22.1-1ubuntu1 \
            # Debugging Tools (used by autograde)
            #   Version locked to ensure parity with autograder.
            valgrind=1:3.18.1-1ubuntu2 \
            # Debugging Tools
            gdb \
            lldb \
            # Miscellaneous
            ca-certificates \
            git \
            imagemagick \
        && rm -rf /var/lib/apt/lists/*

    # Declare an environment variable so we can detect this container.
    ENV CS225_CONTAINER="fa22"

FROM cs225_base_no_catch2 AS cs225_intermediate_builder_catch2
    WORKDIR /tmp/

    # Builds Catch2 from branch v3.1.0 into Catch2.deb
    RUN git clone --branch v3.1.0 --single-branch https://github.com/catchorg/Catch2.git && \
        cd Catch2 && \
        CC=/usr/bin/clang CXX=/usr/bin/clang++ cmake -Bbuild -DBUILD_TESTING=OFF && \
        cmake --build build/ && \
        cd build/ && \
        cpack -G DEB -D CPACK_PACKAGE_FILE_NAME="Catch2"

FROM cs225_base_no_catch2 AS cs225_base
    WORKDIR /tmp/

    # Install Catch2
    COPY --from=cs225_intermediate_builder_catch2 /tmp/Catch2/build/Catch2.deb Catch2.deb
    RUN dpkg -i Catch2.deb && \
        rm -rf Catch2.deb
    
    # Reset WORKDIR
    WORKDIR /root/
