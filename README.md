# LLVM + MLIR packages for Windows

The official LLVM binaries for Windows lack the static libraries, headers and `llvm-config` needed
to build projects that embed LLVM. This repo builds a package containing LLVM, clang, lld **and
MLIR** (targets X86, AArch64, WebAssembly and SPIR-V) so it can be used from Rust with
[inkwell](https://crates.io/crates/inkwell) / [llvm-sys](https://crates.io/crates/llvm-sys) and
[melior](https://crates.io/crates/melior) / [mlir-sys](https://crates.io/crates/mlir-sys).

Extract the archive somewhere (for example `C:\LLVM`) and point the version-specific environment
variables of the crates you use at it, e.g. for LLVM 23:

| Crate | Environment variable |
|-------|----------------------|
| llvm-sys / inkwell | `LLVM_SYS_231_PREFIX=C:\LLVM` |
| mlir-sys / melior | `MLIR_SYS_230_PREFIX=C:\LLVM` |
| tblgen (used by melior's macros) | `TABLEGEN_230_PREFIX=C:\LLVM` |
| bindgen (used by mlir-sys and tblgen) | `LIBCLANG_PATH=C:\LLVM\bin` |

mlir-sys and tblgen generate their bindings with libclang, which has to find the MSVC and Windows SDK
headers. Build melior-based crates from an "x64 Native Tools Command Prompt for VS 2022" (or after
running `vcvars64.bat`); otherwise bindgen fails with `'inttypes.h' file not found`.

The package contains the X86, AArch64, WebAssembly and SPIR-V backends only. inkwell's default
`target-all` feature references every backend, so depend on it with `default-features = false` and
list the targets you need, e.g. `features = ["llvm23-1", "target-x86", "target-aarch64", "target-webassembly"]`.

## Dependencies

- [Visual Studio 2022](https://visualstudio.microsoft.com/vs/community/) with the "Desktop development with C++" workload, to link against this build.
- [7-zip](https://www.7-zip.org/download.html) to unpack.

## Releases

`.github/workflows/check-llvm-releases.yml` polls for new LLVM releases and triggers
`.github/workflows/build.yml`, which builds with Ninja + MSVC and an sccache cache. Because a full
LLVM + MLIR build may not fit in one 6-hour job on a free runner, the build is time-boxed and a
second job resumes from the warm cache when needed. You can also start a build by hand from the
Actions tab with any LLVM version. Download the release artifacts from this repository.

## Building locally

The workflow's CMake invocation lives in `.github/actions/build-llvm/action.yml`; run the same
command from a "x64 Native Tools" developer prompt with CMake and Ninja on `PATH`.
