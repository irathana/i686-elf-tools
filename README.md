# i686-elf-tools
Cross compiling an i386- or i686-elf Win32 toolchain is an outstandingly complicated and painful process. Both Binutils and GCC fail to properly articulate the extent of software dependencies required to build them, resulting in a litany of spurious and confusing error messages being emitted during compilation. Even once you do have the required software dependencies in place, you will still run into roadblocks due to bugs in how GCC performs [canadian cross compilation](https://en.wikipedia.org/wiki/Cross_compiler#Canadian_Cross).

This repo provides a set of precompiled binaries to those who want to use get what they came for and move on (an i686-elf toolchain that, unlike others on the internet, includes cc1 and GDB), as well as a set of instructions for those that would like to build these things themselves. Also featured are a set of instructions for those that wich to install these tools on Mac OS X or Linux.

## Win32
### Tutorial

  By default, `i686-elf-tools.sh` will download GCC 5.3.0, Binutils 2.25 and GDB 7.10. If you would like to change these versions, open the script in your favourite text editor and change the values of `BINUTILS_VERSION`, `GCC_VERSION` and `GDB_VERSION`. Instead of using MinGW32 or MinGW64, [MXE](http://mxe.cc) is used, providing us with an awesome Win32 toolchain that always produces statically linked binaries that just work (and don't need random MinGW DLLs).
  
  Note: if you already have MXE installed, `i686-elf-tools.sh` will not make any attempt to check this, and will automatically download the latest version of MXE to `/opt/mxe` and permanently add another MXE related entry to your `PATH`. Existing MXE users may want to either rename their existing MXE folders to something else, or modify the script so it does not perform any MXE related actions

1. Install a 32-bit (i386) version of Ubuntu. This procedure was performed on top of a Minimal install of Ubuntu 15.10 i386. The Minimal Ubuntu installer can be found [here](https://help.ubuntu.com/community/Installation/MinimalCD). You will need a shell installed in order to run Wine.

2. Download i686-elf-tools.sh to somewhere, e.g. your home directory

3. Make it executable: `chmod +x i686-elf-tools.sh`

4. Run the script: `./i686-elf-tools.sh`

5. If the stars are aligned, everything will go smoothly and after a little while you will have a brand spanking new cross compiler. All required files will automatically be zipped up to an archive `i686-elf-tools.zip` in your current directory. Copy these over to Windows with WinSCP (or your favourite program), unzip, and you should be good to go

6. In the event something does fail, you will need to abort the script (as it performs absolutely zero error checking). You can do this by repeatedly mashing `CTRL+C` until it gives out

### FAQ

#### Why is Wine required?
When your cross compiler has been generated, GCC's `Makefile` will attempt to extract some information from your new compiler by trying to execute it. As your compiler is not built for the system it is being compiled from (in this case, it is meant for Windows) the step will fail and `make` will be interrupted.

While [some claim](http://permalink.gmane.org/gmane.comp.gcc.cross-compiling/15124) GCC actually creates two compilers during the compile process (one for the build system (your Linux OS), one for the host (Windows)), for me this did not appear to be the case. The workaround therefore is to either perform these steps manually, or update the `Makefile` so it is able to run without error. We do this by installing wine, and then telling the `Makefile` to use Wine to execute the required command.

#### When running these steps manually I get an error 'GCC_NO_EXECUTABLES'
The path to the compiler specified as `--host` to `configure` cannot be found on your `PATH`. Update your `.bashrc` and login/logout.

## Mac OS X

Installing an i386-elf toolchain on Mac OS X is an outstandingly simple process, compared to Win32

1. Install [Brew](http://brew.sh/)
2. Download the [i386-elf recepies](https://github.com/altkatz/homebrew-gcc_cross_compilers)
3. Copy `i386-elf-binutils.rb`, `i386-elf-gcc.rb` and `i386-elf-gdb.rb` to `/usr/local/Library/Formula`. As these formulae depend on one another, attempting to execute these directly with `brew install i386-elf-gcc.rb`, etc will fail.
4. Run `brew install i386-elf-binutils`, `brew install i386-elf-gcc` and `brew install i386-elf-gdb`

## Linux

Follow the instructions on the [OSDev Wiki](http://wiki.osdev.org/GCC_Cross-Compiler) or simply remove the `--host` argument to binutils, gcc and gdb's configure script in `i686-elf-tools.sh`