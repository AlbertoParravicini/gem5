# How to setup and use gem5

* Installation guide: http://www.gem5.org/documentation/learning_gem5/part1/building/
    * It's well written, just follow it step by step

* You need `Scons` >= 3.0. If it's not available on your system, download it as `wget http://prdownloads.sourceforge.net/scons/scons-4.0.1.tar.gz`
    * Then, `tar -xf scons-4.0.1.tar.gz; cd SCons-4.0.1; sudo python config.py install`

* You can build using `scons build/X86/gem5.opt -j8`, in most cases (set `-j` to the number of your CPU cores)
    * This binary is build with most optimizations on (e.g. `-O3`), but with debug symbols included. 
* A common error if using Python within Anaconda is `lto1: fatal error: bytecode stream in file 'path/to/libpython3.8.a' generated with LTO version 6.0 instead of the expected 8.1`, with the path and the versions of LTO that depends on your system. It means the Python library has been built with a different version of GCC (in my case, 7.2 vs. 9.3)
    * One way to solve the problem is to disable Anaconda when building gem5
    * Alternatively, try creating a new Anaconda environment with the latest Python (3.9 at this time), e.g. `conda create python=3.9 -n gem5; conda activate gem5`
    * You might also have to install the latest Python system-wide (there's probably a cleaner way to do this): `sudo apt install python3.9; sudo apt install libpython3.9-dev`
    * Then install the package `six` as `conda install six`
    * Remove old gem5 build files and rebuild

# Setup for ARM

* Build gem5 as `scons build/arm/gem5.opt -j8`, this will work also for ARM64
* General guide: http://www.gem5.org/documentation/learning_gem5/part1/extending_configs
    * Links to download pre-build example binaries are broken!
* For Full System (FS) simulation, build `m5term` as `cd util/term; make`
* Then download Linux images from here https://www.gem5.org/documentation/general_docs/fullsystem/guest_binaries
    * Kernel Image: http://dist.gem5.org/dist/current/arm/aarch-system-201901106.tar.bz2
    * Disk Image: http://dist.gem5.org/dist/current/arm/disks/ubuntu-18.04-arm64-docker.img.bz2
    * Store them in a folder such as `arm_fs_images`
    * Add to `~/.bashrc` the path to this folder, as `export IMG_ROOT=~/Documents/gem5/arm_fs_images`

# Running D8

* SE with `build/ARM/gem5.opt configs/tutorial/simple.py --redirects /lib=/home/aparravi/Documents/v8/v8/build/linux/debian_sid_arm64-sysroot/lib/aarch64-linux-gnu` doesn't seem to work, it still complains that `Failed to open file /lib/ld-linux-aarch64.so.1.`
    * https://stackoverflow.com/questions/64547306/cannot-open-lib-ld-linux-aarch64-so-1-in-qemu-or-gem5
    * https://stackoverflow.com/questions/50542222/how-to-run-a-dynamically-linked-executable-syscall-emulation-mode-se-py-in-gem5/50696098#50696098

* Example of Full System simulation
    * If running `fs_bigLITTLE.py`, change `/dev/vda1` to `/dev/vda` around line 170 (the default boot disk option value)
    * Start FS as `./build/ARM/gem5.opt configs/example/arm/fs_bigLITTLE.py --caches --bootloader=$IMG_ROOT/binaries/boot.arm64 --kernel=$IMG_ROOT/binaries/vmlinux.arm64 --disk=$IMG_ROOT/disks/m5_exit.squashfs.arm64`
    * Then open another terminal and connect to the simulation as `./util/term/m5term 3456`
