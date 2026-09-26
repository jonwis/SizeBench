The current version of DIA: Visual Studio 2026 18.7.3 (June 2026 Feature Update)

msdia140.dll (amd64) is now vendored directly in this folder, taken from Visual Studio 18
Enterprise's "DIA SDK\bin\amd64\msdia140.dll", file version 14.51.36257.0. Redistributing
msdia140.dll unmodified alongside a tool that consumes it is covered by the Distributable
Code terms of the Visual Studio license, the same terms that let SizeBench (and countless
other PDB tools) ship it at all.

We moved off of getting msdia140.dll from the Microsoft.Debugging.Platform.DbgEng NuGet
package because that package's copy (msdia140.dll 14.44.35222.100, as of the
20260319.1511.0 package) predates DIA's support for the newer, compressed "MSFZ"/PDZ PDB
container format: asking it to open an MSFZ-format PDB via loadDataFromPdbEx hangs
indefinitely instead of returning or throwing. The DIA SDK's own msdia140.dll (14.51.36257.0,
first available starting with Visual Studio 2022 17.12) opens MSFZ-format PDBs correctly.
See DIAAdapter.cs and SessionTests.cs for the MSFZ regression test.

Here's how to take a new drop of DIA:
1. Get msdia<version>.dll from somewhere (e.g. "<VS install>\DIA SDK\bin\amd64\msdia140.dll").
   Copy it into this folder, replacing msdia140.dll.
2. Get dia2.idl that matches that DLL, and all the headers (cvconst.h, etc.)
3. Update this file to indicate what location/build you took the new drop from.
4. Run these commands:
   cd <SizeBenchRoot>\ExternalDependencies\DIA
   midl dia2.idl /tlb dia2.tlb
   tlbimp dia2.tlb
7. Open the resulting Dia2Lib.dll in Visual Studio's Object Browser, or ILSpy, verify it has the new things you expect