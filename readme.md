# Snap7 1.4.3 - DYNACOM fork with client-side security fixes

**This is not the official repository.** It is a fork of
[davenardella/snap7](https://github.com/davenardella/snap7) at release 1.4.3
(commit `30f37da`), maintained by DYNACOM s.r.l., with fixes for memory-safety
defects in the S7 **client** that were reported on the official repository and
are still open there.

Branch `dynacom-fixes` = official 1.4.3 + the commits below. The public ABI is
unchanged (same 127 exported functions, same records): the library is a drop-in
replacement.

## What is fixed

| Upstream issue | Defect | Fixed in |
|---|---|---|
| [#31](https://github.com/davenardella/snap7/issues/31) | `opReadArea`: copy length taken from the answer, write past the caller's buffer | `a8fde0a` |
| [#32](https://github.com/davenardella/snap7/issues/32) | `opReadMultiVars`: same defect, per item | `a8fde0a` |
| [#33](https://github.com/davenardella/snap7/issues/33) | `isoRecvPDU`: fragment reassembly writes past the ISO payload | `b90d51c` |
| [#17](https://github.com/davenardella/snap7/issues/17) | `opWriteArea`: negotiated PDU length accepted without an upper bound | `b90d51c` |
| [#11](https://github.com/davenardella/snap7/issues/11) | `opReadSZL`: slice length declared by the peer not checked | `b90d51c` |

`a8fde0a` also makes read and write jobs reject an answer that does not belong
to the request (different sequence number or function). Without it, after a
receive timeout a late answer is taken as the answer to the next request.

After `errCliInvalidPlcAnswer` or `errCliInvalidDataSizeRecvd` the stream is out
of step: the application must disconnect and reconnect.

## What is NOT fixed

- Server side: [#14](https://github.com/davenardella/snap7/issues/14)
  (CVE-2026-51218), [#16](https://github.com/davenardella/snap7/issues/16),
  CVE-2026-15105. Do not expose a Snap7 server to an untrusted network.
- [#30](https://github.com/davenardella/snap7/issues/30) (`opUpload`) and
  [#15](https://github.com/davenardella/snap7/issues/15) (partner).

## Licence

LGPL v3 or later, as the original (see `lgpl-3.0.txt`). The text of the original
readme follows.

---

# Snap7 (original readme)

This is the **official** Snap7 repository starting with release 1.4.3.

Previous releases are available in the <a href="https://sourceforge.net/projects/snap7/files/" target="_blank">SourceForge</a> repository.

The official site is still <a href="https://snap7.sourceforge.net/" target="_blank">here</a>.

---

To make downloading and compiling more efficient on different operating systems, the package has been split.

You will find the old folders as separate repositories, so you can download only what you are interested in.

This is the list:

| Folder | Repository   |
|----------|------------|
| /snap7/examples   | <a href="https://github.com/davenardella/snap7.examples" target="_blank">snap7.examples</a> |
| /snap7/LabVIEW | <a href="https://github.com/davenardella/snap7.LabVIEW" target="_blank">snap7.LabVIEW</a> |
| /snap7/rich-demos | <a href="https://github.com/davenardella/snap7.rich-demos" target="_blank">snap7.rich-demos</a> |
| /snap7/utility | <a href="https://github.com/davenardella/snap7.utility" target="_blank">snap7.utility</a> |

in <a href="https://github.com/davenardella/snap7.utility" target="_blank">snap7.utility</a> you will also find **HMITracer**, **clientdemo**, **serverdemo** and **partnerdemo**, already compiled for Windows, useful for testing the connection to a CPU/CP or trace what an external HMI is requesting to a PLC.

You can find further info in History.txt

---

## Build Snap7

The **/build/bin/win32** and **/build/bin/win64** folders already contain the latest Windows DLLs (and .lib), ready to be used.
For other (*nix) operating systems you need to compile snap7 to match your architecture, libc version, etc. 

To do this you need the C++ toolchain, which is usually already included in the distribution.


Otherwise, for example on Linux, you can install it with:

`sudo apt install build-essential`

### *nix OS (Linux, BSD, OSX, Solaris Intel/Sparc, MIPS)

Download and unpack this repository in your home, or use git:

`git clone https://github.com/davenardella/snap7.git`

Enter in the OS folder (snap7/build/< your OS >/), then:

#### Linux, MIPS:

`make [clean | all | install]`

#### OSX, BSD:

`gmake [clean | all | install]`

#### Solaris 32 bit using Oracle Solaris Studio

`gmake -f i386_solaris_cc.mk [clean | all | install]`

#### Solaris 64 bit using Oracle Solaris Studio

`gmake -f x86_64_solaris_cc.mk [clean | all | install]`

#### Solaris 32 bit using GNU Toolchain

`gmake -f i386_solaris_gcc.mk [clean | all | install]`

#### Solaris 64 bit using GNU Toolchain

`gmake -f x86_64_solaris_gcc.mk [clean | all | install]`

>***Note: install switch requires* sudo**

### Windows 

Open the Solution file (/build/windows/VSXXXX/VSXXXX.sln) with **Visual Studio** (Community Edition is free and fine) then build it.
