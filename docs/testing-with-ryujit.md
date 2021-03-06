# .NET Framework 4.6 - Testing with RyuJIT

The .NET Framework 4.6 includes new Just-In-Time (JIT) compiler for 64-bit processes, called RyuJIT. It is enabled by default. It is still a preview version, so you may discover issues that have yet to be fixed.

You may experience the following symptoms when you use the new JIT compiler:

* An application throws an `InvalidProgramException` error.
* An application works on an x86-based computer but not on an x64-based
  computer.

## Workarounds for JIT Compilation

To work around this problem, use any one of the following methods. 

**Important** Follow the steps in this section carefully. Serious problems might
occur if you modify the registry incorrectly. Before you modify it,
[back up the registry](http://support.microsoft.com/kb/322756) for restoration
in case problems occur.

**Note** For all these methods, all dynamic compilation is performed by the older
JIT64 JIT. Also, all NGEN compilation continues to use the new JIT, and all
existing NGEN images that have been compiled by the new JIT continue to be used.

* **Method 1**. Set the following environment variable:

        COMPLUS_useLegacyJit=1

* **Method 2**. In the registry, create either of the following subkeys:

        HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework
        HKEY_CURRENT_USER\SOFTWARE\Microsoft\.NETFramework

  Then, specify the following:

        Key name: useLegacyJit
        Type: REG_WORD
        Value: 1

* **Method 3**. Add the following text to the `<app>.exe.config file`. Create
  the indicated sections if they do not already exist.

  **Note** In this file name, `<app>` represents the actual name of the
  application.

        <configuration>
          <runtime>
           <useLegacyJit enabled="1">
          </runtime>
        </configuration>

## Workarounds for NGEN Compilation

If you encounter a bug when you use the new JIT, and if the bug manifests itself
as an NGEN image, use any of the following methods to force certain named
assemblies to be recompiled by the JIT and not use the existing native images:

* **Method 1**. Set the following environment variable:

        COMPLUS_DisableNativeImageLoadList=assembly_one;assembly_two;assembly_three

* **Method 2**. In the registry, create either of the following subkeys:

        HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework
        HKEY_CURRENT_USER\SOFTWARE\Microsoft\.NETFramework

  Then, specify the following:

        Key name: DisableNativeImageLoadList
        Type: REG_SZ
        Value: assembly_one;assembly_two;assembly_three

  **Note** This is a semicolon-delimited or space-delimited list of simple
  assembly names (no public key token, no architecture, and so on). This list
  does not contain the `.dll` file name extension. In the examples in this
  method and the next method, `assembly_one` is the simple name for an assembly
  that is named `assembly_one.dll`.

* **Method 3**. Add the following text to the `<app>.exe.config` file. Create
  the indicated sections if they do not already exist.

  **Note** In this file name, `<app>` represents the actual name of the
  application.

      <configuration>
        <runtime>
         <disableNativeImageLoad>
          <assemblyIdentity name="assembly_one" />
          <assemblyIdentity name="assembly_two" />
        </disableNativeImageLoad>
        </runtime>
      </configuration>