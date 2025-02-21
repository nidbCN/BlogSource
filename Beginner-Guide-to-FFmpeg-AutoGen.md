---
title: 'A Beginner's Guide to FFmpeg.AutoGen'
author: Gaein nidb
categories:
  - 代码如诗
tags:
  - CSharp
  - DotNet
  - FFmpeg
  - 多媒体编解码
date: 2025-02-22 00:35:00
lastmod: 2025-02-22 00:35:00
---

# A Beginner's Guide to FFmpeg.AutoGen

## Introduction

FFmpeg.AutoGen is an auto-generated unsafe bindings for C#/.NET and Mono. It can be used in C#/.NET to invoke ffmpeg's C/C++ libraries.

The usage is the same as C/C++ API so you can refer to ffmpeg official documents. However, in C#/.NET, we need to load the libraries first, which confused me for a long time.

Here I concluded the some useful information for beginners to start using ffmpeg.AutoGen. 

This guide addresses the following questions:

* which nuget package should I use?
* how to configure the libraries root path?
* how to load libraries from system default path?
* how to solve `System.NotSupportedException`?

## which nuget package should I use? - ffmpeg.AutoGen packages

### packages list

FFmpeg.AutoGen has the following packages on NuGet:

* [FFmpeg.AutoGen](https://www.nuget.org/packages/FFmpeg.AutoGen)
* [FFmpeg.AutoGen.Abstractions](https://www.nuget.org/packages/FFmpeg.AutoGen.Abstractions)
* [FFmpeg.AutoGen.Bindings.DynamicallyLoaded](https://www.nuget.org/packages/FFmpeg.AutoGen.Bindings.DynamicallyLoaded)
* [FFmpeg.AutoGen.Bindings.DynamicallyLinked](https://www.nuget.org/packages/FFmpeg.AutoGen.Bindings.DynamicallyLinked)
* [FFmpeg.AutoGen.Bindings.StaticallyLinked](https://www.nuget.org/packages/FFmpeg.AutoGen.Bindings.StaticallyLinked)

### Introduct to each packages

#### FFmpeg.AutoGen.Bindings.DynamicallyLinked

`FFmpeg.AutoGen.Bindings.DynamicallyLinked` uses `DllImport` to load libraries.

It loads libraries from the default path.

But the file name passed to `DllImport` is hard-coded in Windows file naming convention, such as something like "avutil-59". That means you **can not use this package directly on other platforms**. But you can implement a custom resolver to adapt the filename convention from windows to others platforms by using the code below:

```cs
public static class LibraryUtil
{
    public static IntPtr LinuxFfMpegDllImportResolver(string libraryName, Assembly assembly, DllImportSearchPath? searchPath)
        => FfMpegDllImportResolverCore(libraryName, assembly, searchPath, "so");

    public static IntPtr MacOsFfMpegDllImportResolver(string libraryName, Assembly assembly, DllImportSearchPath? searchPath)
        => FfMpegDllImportResolverCore(libraryName, assembly, searchPath, "dylib");

    public static IntPtr FfMpegDllImportResolverCore(ReadOnlySpan<char> loadNameSpan, Assembly assembly, DllImportSearchPath? searchPath, string extension)
    {
        var partedIndex = loadNameSpan.IndexOf('-');

        // contains 0 or multi '-', format not ffmpeg library
        if (partedIndex == -1 || loadNameSpan.LastIndexOf('-') != partedIndex)
            return NativeLibrary.Load(loadNameSpan.ToString(), assembly, searchPath);

        var libraryName = loadNameSpan[..partedIndex];

        // library not in map table, not ffmpeg library
        if (!DynamicallyLoadedBindings.LibraryVersionMap.ContainsKey(libraryName.ToString()))
            return NativeLibrary.Load(loadNameSpan.ToString(), assembly, searchPath);

        var versionName = loadNameSpan[(partedIndex + 1)..];

        var styledName = $"{libraryName}.{extension}.{versionName}";
        return NativeLibrary.Load(styledName, assembly, searchPath);
    }
}
```

And using thos codes to configure DllImport use custom resolver:

```cs
// instantiate the logger before here or change `logger.LogInformation` to `Console.WriteLine`

if (!RuntimeInformation.IsOSPlatform(OSPlatform.Windows))
{
    // not windows
    var bindingAssembly = typeof(DynamicallyLinkedBindings).Assembly;
    if (RuntimeInformation.IsOSPlatform(OSPlatform.Linux)
        || RuntimeInformation.IsOSPlatform(OSPlatform.FreeBSD))
    {
        logger.LogInformation("Linux/FreeBSD platform, use resolver {name} for assembly `{asm}`.", nameof(LibraryUtil.LinuxFfMpegDllImportResolver), bindingAssembly.GetName());
        NativeLibrary.SetDllImportResolver(bindingAssembly, LibraryUtil.LinuxFfMpegDllImportResolver);
    }
    else if (RuntimeInformation.IsOSPlatform(OSPlatform.OSX))
    {
        logger.LogInformation("OSX platform, use resolver {name} for assembly `{asm}`.", nameof(LibraryUtil.LinuxFfMpegDllImportResolver), bindingAssembly.GetName());
        NativeLibrary.SetDllImportResolver(bindingAssembly, LibraryUtil.MacOsFfMpegDllImportResolver);
    }
    else
    {
        throw new PlatformNotSupportedException();
    }
}
```

#### FFmpeg.AutoGen.Bindings.DynamicallyLoaded

`FFmpeg.AutoGen.Bindings.DynamicallyLoaded` use `LoadLibrary` method in `Kernel32` library on Windows, use `dlopen` method in `Libdl` on Linux and MacOS.

#### FFmpeg.AutoGen.Bindings.StaticallyLinked

This package use `DllImport("__Internal")` to import libraries, it required you build the library into assembly.

#### FFmpeg.AutoGen.Abstractions

This package contains definitions about structs and functions. But you can't invoke functions define in this package, because it's only have definitions but no body, you need install others `Bindings` package and use `DynamicallyLinkedBindings.Initialize();` or `DynamicallyLoadedBindings.Initialize();` method to initialize.

#### FFmpeg.AutoGen

This package looks like a combine of `FFmpeg.AutoGen.Abstractions` and `FFmpeg.AutoGen.Bindings.DynamicallyLoaded` with a few properties changed. 

The offical example use ``FFmpeg.AutoGen.Bindings.DynamicallyLoadedBindings` instead of `FFmpeg.AutoGen`, but the README use `ffmpeg.RootPath = ` to set libraries path which is defined in `FFmpeg.AutoGen`.

### conclusion of which package should I choose

* If you're using ffmpeg in Mono, choose `FFmpeg.AutoGen.Bindings.StaticallyLinked` and `FFmpeg.AutoGen.Abstractions`.

* If your app only run on Windows, choose `FFmpeg.AutoGen.Bindings.DynamicallyLinked` and `FFmpeg.AutoGen.Abstractions`.

* If your app running on mult-platform, choose `FFmpeg.AutoGen.Bindings.DynamicallyLoaded` and `FFmpeg.AutoGen.Abstractions` or `FFmpeg.AutoGen.Bindings.DynamicallyLinked` with custom resolver. Use `FFmpeg.AutoGen` is also a good choice.

## how to configure the libraries root path? - use prop in DynamicallyLoaded or FFmpeg.AutoGen

`DynamicallyLinked` can't specify a custom root path for libraries.

If you want to use the library files which are not in the default search path, use `DynamicallyLoaded` and set the path to property `DynamicallyLoadedBindings.LibrariesPath`.

`ffmpeg.RootPath` from package `FFmpeg.AutoGen` is the same as `DynamicallyLoadedBindings.LibrariesPath`.

By the way, the libraries root should contains libs like `avutil-59.dll` on Windows, and `libavutil.59.so` on Linux.

## hot to load libraries from system default path - just do nothing

In the previous section, I explained the libraries load function of each packages. According to Microsoft Docs and Linux manual page we can find the search order.

### Search order on Windows and Linux

On windows, the main search order are:

1. Application directory;
2. System directory;
3. Windows directory;
4. Current work directory;
5. Directories in the `PATH` environment variable;

For more information, see reference:

* [Dynamic-link library search order - Win32 apps | Microsoft Learn](https://learn.microsoft.com/en-us/windows/win32/dlls/dynamic-link-library-search-order)

On Linux, the main search order are:

1. Directories in the `LD_LIBRARY_PATH` environment variable;
2. `/etc/ld.so.cache` directory;
3. `/lib` and `/usr/lib` directories.

For more information, see reference:

* [dlopen(3) - Linux manual page](https://man7.org/linux/man-pages/man3/dlopen.3.html#DESCRIPTION)
* [ld.so(8) - Linux manual page](https://man7.org/linux/man-pages/man8/ld.so.8.html#DESCRIPTION)
* [FFmpeg.AutoGen/FFmpeg.AutoGen.Bindings.DynamicallyLoaded/Native at master · Ruslan-B/FFmpeg.AutoGen](https://github.com/Ruslan-B/FFmpeg.AutoGen/tree/master/FFmpeg.AutoGen.Bindings.DynamicallyLoaded/Native)

I have no computer with MacOS, so I haven't figured out the search order on MacOS, but I guess it maybe very like the order on Linux.

If you want to use `DynamicallyLoaded` with default path, just do not modify `DynamicallyLoadedBindings.LibrariesPath` or ensure its value are `string.Empty`. This prop will be added to the front of library name and version. Keep it empty and the package will deliver pure library file name to the method which load the library to memory, that will lead the load method use default search path.

## hot to solve `System.NotSupportException`? - a few debug tips

If you invoke a `ffmpeg.*` function and throw this exception, it is often because you didn't load the libraries successfully.

First, make sure you install the `FFmpeg.AutoGen.Bindings` or `FFmpeg.AutoGen` and called the `Initialize()`.

Second, make sure your libraries files are in correct path with correct file name. If you use the custom root path, just open it and have a look, If you use system default path, try using the system tools to ensure it can be found, such as `ldconfig -p -N | grep lib<name>.<version>`.

Third, make sure all libraries are fine. You can set `DynamicallyLoadedBindings.ThrowErrorIfFunctionNotFound` to `true`, then the exception will sent during libraries loading. I suggess you print all libriaries version to ensure they are all loaded with the code below:

```cs
// instantiate the logger before here or change `logger.Log*` to `Console.WriteLine`

try
{
    var version = ffmpeg.av_version_info();
    var libraryVersion = new StringBuilder(48 * DynamicallyLoadedBindings.LibraryVersionMap.Count);

    foreach (var (library, requiredVersion) in DynamicallyLoadedBindings.LibraryVersionMap)
    {
        var versionFunc = typeof(ffmpeg).GetMethod($"{library}_version");
        var versionInfo = new VersionInfo((uint)(versionFunc?.Invoke(null, null) ?? 0u));

        libraryVersion.AppendLine($"\tLibrary: {library}, require `{requiredVersion}`, load `{versionInfo}`");
    }

    libraryVersion.Remove(libraryVersion.Length - 1, 1);    // remove '\n'

    logger.LogInformation("Load ffmpeg, version `{v}`", version);
    logger.LogInformation("Load ffmpeg, libraries:\n{libInfo}", libraryVersion);
}
catch (NotSupportedException e)
{
    logger.LogCritical(e, "Failed to load ffmpeg, exit.");
    var appLifeTime = provider.GetRequiredService<IHostApplicationLifetime>();
    appLifeTime.StopApplication();
}
```

You can also just add the project source code as a dependency of your project and debug in file `FunctionResolverBase.cs` or files under `Native` folder.
