# LLVM packages for Windows

The official LLVM binaries only contain a small number of binaries and required files, but lack some that enable integrating LLVM in new projects. This repo contains the "projects" required to support inkwell development on Windows. Extract the content to "C:\LLVM" and create the required "LLVM_SYS_" environment pointing to that folder. For example, set "LLVM_SYS_180_PREFIX" to "C:\LLVM".

## Dependencies

- Requires [Visual Studio 2022](https://visualstudio.microsoft.com/vs/community/) with the "Desktop development with C++" workload installed on the Windows computer to enable usage of this LLVM build.
- Requires [7-zip](https://www.7-zip.org/download.html) to unpack.


The build script uses [Scoop](https://scoop.sh/) to install some required dependencies. If you
already have these dependencies scoop will not overwrite them.

## Releases

New builds are built automatically when the `.github/workflows/build.yml` `env.llvm_version` is updated.
Download the release artifacts from this repository.

## Local build

To build LLVM locally, run:

```powershell
$ .\build.ps1
```

The script will build and package LLVM.
