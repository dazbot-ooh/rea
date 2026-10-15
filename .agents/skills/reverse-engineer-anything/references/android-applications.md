# Android application artifacts

Use the Android tools that the connected server advertises. Repository main and npm 4.1.0 include this family. A skill installation alone cannot add it to an older server.

Start with `inspect_android_package` on the APK path of the caller. It returns package identity, manifest declarations, coverage, and inline Evidence. Then use these tools:

- `search_android_classes` finds candidates.
- `inspect_android_class` shows the members of one class.
- `inspect_android_method` gives decompilation.
- `trace_android_references` gives incoming static references.

Each call takes its own APK path and explicit selectors. Do not open a native session first. When methods are ambiguous, choose an `overload_index` from the class inventory. Do not guess.

The provider needs these items, which you supply separately:

- a headless JADX MCP JAR 0.7.1
- Java 17 or newer

`REA_JADX_MCP_JAR` selects the absolute JAR path. `JAVA_HOME` can select Java. REA setup does not install or configure that JAR. Doctor provider scopes cover Hopper/Ghidra, not JADX. Use existing tools. Report the missing prerequisites for the Android task specifically.

Linux and macOS use the POSIX owned-process boundary. Windows x64 needs the matching bundled native controls. It uses an owned Job Object with bidirectional stdio. Every host needs a full JDK, including `jdk.compiler`. On Windows, `JAVA_HOME` selects `bin\java.exe`. Read the Android guide for the actual host verification coverage.

Calls use one worker and provider deadlines. The optional `REA_JADX_HEAP_MIB` selects the JVM heap. REA never launches the APK. No emulator is necessary.

Manifest entries and decompiled text are static observations. They are not execution or policy enforcement. Incoming references are static relationships that REA recovers. Reflection, dynamic loading, and unresolved calls remain unknown. Preserve artifact digests, locations, diagnostics, and incomplete coverage. For archive members instead of code analysis, use `open_binary` and `inspect_artifact`.
