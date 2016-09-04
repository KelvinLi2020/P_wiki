## Dependencies
Dependencies to build P on Ubuntu are mono(>=4.5) and cmake(>=2.8)

Currently to install mono with the required version, one has to compile from source; please refer to [mono installation](http://www.mono-project.com/docs/compiling-mono/) for installation instruction for your platform.

You can find info about installing cmake [here](https://cmake.org/install/)

## Building P Compiler
- Once you have installed the dependencies, you should be able to compile P with just:

```{r, engine='bash', count_lines}
  $ cd Bld; ./build.sh
```

- From time to time if you would like to compile just P.sln, you can also invoke xbuild manually:

```{r, engine='bash', count_lines}
  $ xbuild
``` 


Note that to avoid errors because of case-sensitive paths on some distribution of Linux systems, mono provides an option which is controlled by the *MONO_IOMAP* environment variable. You need to execute the following command to set the environment variable before building 

```{r, engine='bash', count_lines}
  $ export MONO_IOMAP=case
``` 

You may also consider add the above export command to your `bashrc`. You can get more info [here](http://www.mono-project.com/archived/porting_msbuild_projects_to_xbuild/)

- Once above succeeds, you should expect Pc.exe(the P Compiler executable) under Bld folder. You can now try to run the compiler with 
```{r, engine='bash', count_lines}
  $ mono Bld/Pc.exe
```

## Building Prt
- Currently, the mono build system with CMake only supports building the Prt runtime. The CMake is automatically invoked by build.sh. Both a shared and a static library of Prt are built and copied to Bld/Drops/PrtUser

- If you would like to invoke the CMake manually, you can do
```{r, engine='bash', count_lines}
  $ mkdir build; cd build # out of source build folder
  $ cmake path/to/P/Src/CMakeLists.txt 
  $ make
```

## Caveats
- Currently, the build process just ignores .vcprojs, and these C/C++ components of P are built using CMake.