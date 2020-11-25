# [UDK2017]( https://github.com/tianocore/tianocore.github.io/wiki/UDK2017) BaseTools Notes
1. [Introduction](#introduction)
2. [New Features and Enhancements](#new-features-and-enhancements)
3. [Bug Fixes](#bug-fixes)
4. [Known Issues](#known-issues)
5. [Notes](#notes)
6. [Best Known Methonds](#best-known-methods)


##  INTRODUCTION
This is the primary set of tools for processing EDK II content. It contains configuration templates and source files.
The tools support a Makefile based EDK II build with no additional packages required--the compiler tool chain, an assembler
and optional ACPI assembler are the only additional tools need to build the EDK II project.

Source code is divided into two types:
1) Tools written in C (ANSI C) are primarily for tools that modify binary data structures.
2) Tools based on Python (Python) are primarily for tools that parse or process text files.

##  NEW FEATURES AND ENHANCEMENTS
1.  Add new command line option to support override PCD value
2.  Add mixed PCD support feature
3.  Add Windows batch files to run python tool from Source
4.  Support private package definition
5.  Add support to merge Prebuild and Postbuild into build Process
6.  Support generating image package from BMP/JPEG/PNG files
7.  Extend the RAW format to support multiple binary files
8.  Add two new sections(Conditional directives section, Unused PCDs section) for PCD in the build report
9.  Add a VPD report subsection of FLASH to the Report
10. Add a alignment check based on value type for VOID* PCDs in VPD region
11. Add support for INF statement in FD region
12. Build Tools support multiple workspace
13. Support PCD value to use expression in the DEC file
14. Follow PI1.4a to fix artificial limitation of PCD SkuId range
15. Enhance BaseTools supports FixedAtBuild usage in VFR file
16. Cache the defined Guid tool to improve the performance
17. Skip module AutoGen by comparing timestamp
18. Update FMP Capsule support to follow FDF spec
19. FMP capsule add the support to generate auth info
20. Support FDF sections in any order
21. Extend the Macro used in the FDF !include statement
22. Improve shell wrappers for Python build commands
23. Improve shell wrapper for C build commands
24. VolInfo: generate HASH value for each PE image
25. Generate hash value in build report for each output EFI image
26. Support \x####\ in UNI files to specify non-ascii characters
27. Update Tools and Scripts to support VS2015 env
28. Add PatchCheck.py script
29. Add the PKCS7 tool
30. Copy Brotli algorithm 3rd party source code for tool
31. Rsa2048Sha256Sign add new option to support Monotonic count
32. LzmaCompress: Update LZMA to new 16.04 version
33. Add '!include' support to tools_def.txt parser
34. tools_def: enable Os optimization for GCC X64 builds
35. introduce GCC5 toolchain to support GCC v5.x in LTO mode
36. Introduce CLANG38 new toolchain for x86
37. Introduce PREFIX env for VS tool path
38. Add -march=i586 for IA32 GCC targets
39. AARCH64: override XIP module linker alignment to 32 bytes
40. Makefile: Enable O2 option to replace Od for VS tool chain

##  BUG FIXES
1.  PeCoffLoader: Fix handling of ARM MOVW/MOVT instruction relocs
2.  Fix the issue to support windows root directory
3.  Add extern protocol/PPI/GUID definition in AutoGen for Library
4.  Add new option to not zero PE/COFF optional header fields
5.  Add build error detection for Dynamic PCD name conflict
6.  Fix nmake failure due to command-line length limitation
7.  Fix a bug when apply patches to SEC use the FILE_GUID override
8.  Process the files by the priority in BUILDRULEORDER
9.  Fix the bug when no FD section in the FDF file
10. Fix the bug for VOID* Patchable PCD declaration in Library
11. Incremental build not work if VPD values in DSC changed by -D
12. AARCH64: add support for relative data relocations
13. Fix the bug for wrong alignment generate for RAW file
14. Update to handle PE image with .code section only
15. Fix a bug for PEI VPD Pcd collection
16. Fix bad macro expansion during tools_def.txt parsing
17. Fix a bug for FixedPcd value generation in AutoGen file
18. Fix the bug when use FILE_GUID override the module in DSC
19. GccBase.lds: don't copy RELA section to PE/COFF
20. Fix the bug for OptionRom generation with different arch
21. Fix the Windows GCC Build Failure with too long path
22. Avoid possible NULL pointer dereference
23. Fix parameter format mismatch in scanf functions
24. Add checks for array access
25. Add checks for user/file inputs
26. Fix potential memory leak
27. Fix file handles not being closed
28. Fix the bug to support generate FFS by Align=Auto
29. Fix the bug to parse the new map file format
30. Fix the bug to parse the short varname in map file
31. Fix bug for GUIDED tool path override by DSC [BuildOptions]
32. Fix build failure for DynamicEx Pcd used in the Library
33. Update OPENSSL_PATH to support space characters
34. Correct VOID* PatchPcd Size in Library Autogen
35. Fix the bug that FixedPcdGetPtr failure for CArray Pcd
36. Fix the bug for CArray PCD override in command line
37. Fix incremental build failure that override file be removed
38. Fix incremental build bug on DynamicPcd Token Generation
39. Fix the bug use same FMP_PAYLOAD in different capsule file

##  KNOWN ISSUES
1.  Use multiple "!if" or "!endif" statements in external file will cause build break.
An example that will break the build:
<pre>
    In one of DSC section, use the following !include statement such as:
    [Components]
    !include externalFile.txt
    !endif
    !endif
    and in externalFile.txt, the content is:
    !if True
    !if True
    TestPkg1/TestModule1/Test1.Inf
</pre>    
2.  If you do not specify build target information both in command line and Conf\target.txt file, build tool will exit build
    immediately.
3.  PCD used in FDF conditional directive statement without SET statement define this PCD in FDF file will cause build break.
    An example as following:
<pre>
    In DEC file:
    [PcdsFixedAtBuild]
      gEfiPcdname.Pcdtoken|0x0|UINT32|0x11112222
    IN DSC file:
    [PcdsFixedAtBuild]
      gEfiPcdname.Pcdtoken|0x0
    IN FDF file:
    [FD.NT32]
    !if gEfiPcdname.Pcdtoken == 0x00000000
      XXXXXXXXXXX
    !endif
    Another Example is:
    [FD.Nt32]
      BaseAddress   = 0x0|gEfiNt32PkgTokenSpaceGuid.PcdWinNtFdBaseAddress
      !if gEfiNt32PkgTokenSpaceGuid.PcdWinNtFdBaseAddress
        xxxxx
      !endif
</pre>    
4.  If building with tool chain DDK3790 and ARCH X64, and HII resource is generated by RC tool, build will break. But BaseTools
    project has added this support at revision 2656. The workaround is add WINDDK_BIN32 into %PATH%.
5.  The target path name cannot exceed 256 characters for nmake, long file path feature can only support pure binary build
    which does not generate Makefile.

##  NOTES
1.  Macros referenced in DSC/FDF files now requires the enclosure of "$()" to avoid ambiguity.
2.  EFI_SPECIFICATION_VERSION and PI_SPECIFICATION_VERSION are no longer public macro definitions in autogen code.
3.  The feature of CLM support increases the chances of two platform builds failure under one workspace. We need to
    avoid the concurrent build of different platforms under one workspace.
4.  The execution order prediction feature of BRG requires the new internal python tool named EOT. It requires the
    installation of ANTRL v3.0.1 in Python home for Python runtime support. The tool parses all the source files
    involved in the platform build and it may even take hours to complete the parse and generate the report.
5.  The blank setting for CAPSULE_FLAGS in [Capsule] section of FDF file is not supported. If no CAPSULE_FLAGS is set,
    CAPSULE_FLAGS need not be specified in [Capsule] section.
6.  The Global Macro is not allowed in EDKII INF except some predefined cases; see details in INF spec 1.26 chapter 2.2.6.
7.  The expression after !if, !ifdef, !elseif cannot be empty.
8.  The section header in FDF cannot contain macros.
9.  The name and value definition in define section must follow DSC spec.
10. The type of a PCD defined in a DSC file should match the possible types for this PCD in the DEC file.
11. For MACRO usage scope, please refer to meta-data file spec for details. Following is some general information.
    INF - [Defines] section or any statements contain <PATH> or [BuildOptions] section
    DEC - [Defines] section or any statements contain <PATH>
    DSC - [Defines] section or any statements contain <PATH> or [BuildOptions] or Conditional Directive statements
    FDF - Whole File
12. VfrCompile tool adds data type check for Oneof and Numeric opcode. The valid data type for Oneof and Numeric question
    are UINT8, UINT16, UINT32 and UINT64.


##  BEST KNOWN METHODS
1.  When the build tools are updated, the build configuration template in $(WORKSPACE)\BaseTools\Conf\*.template may also
    be updated, which needs to be merged with local $(WORKSPACE)\Conf\*.txt to avoid some build error.
2.  To ensure the platform is clean rebuild without any previous build impact, the cached meta data information directory
    i.e.$(WORKSPACE)\Conf\.cache can be removed.
3.  In case of build failure due to access error of $(WORKSPACE)\Conf\.cache\build.db, the tip can be rebuilt again without
    any concurrent build process under the same workspace.
4.  When the different MACRO settings are used to enable the different drivers with same module GUID and driver name in
    two build times, the incremental build will not work well. The clean build is required for the second build.
5.  The EOT tool depends on ANTRL 3.0.1 runtime library and newer ANTRL release (e.g. 3.1.2) is not compatible with older
    release. So it is required to download ANTLR 3.0.1 from www.antlr.org and install python runtime library to the local
    python home.
6.  The notification function related features are not integrated to BRG output plain text report file. As the invocation
    of EOT (from BRG) generates Report.html under workspace directory. This HTML report file shows the details of the notification
    functions under each module if available.
7.  The Prebuild and Postbuild entry must be a tool that can be executed.
