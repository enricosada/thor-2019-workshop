---
permalink: /requirements/
---

# Requirements

- [.NET Core Sdk 2.1](#dotnetsdk)
- [Mono](#mono) (on unix/mac)
- [Visual Studio Code](#vscode)
  - C# extension
  - Ionide-fsharp extension
- [Docker and images](#docker) (optional)

Any v2.0.x (sdk, runtime, docker, etc) is ok. Instructions show latest avaiable.

<a name="dotnetsdk"></a>
## .NET Core Sdk 2.1

Install the Sdk (not the Runtime):

[https://www.microsoft.com/net/download/core#/sdk](https://www.microsoft.com/net/download/core#/sdk)

In the page there are also the `Step-by-step instructions`

Prerequisites:

- [linux](https://docs.microsoft.com/en-us/dotnet/core/linux-prerequisites?tabs=netcore2x)
- [osx](https://docs.microsoft.com/en-us/dotnet/core/macos-prerequisites)
- [windows](https://docs.microsoft.com/en-us/dotnet/core/windows-prerequisites?tabs=netcore2x)

Check if is installed correctly with `dotnet --info` should print:

```
.NET Command Line Tools (2.1.103)

Product Information:
 Version:            2.1.103
 Commit SHA-1 hash:  60218cecb5

<additional info about os>
```

<a name="mono"></a>
## Mono on unix/mac

For unix/mac, Windows doesnt need it.

`NOTE` it's a prerequisite of Ionide extension in VS Code, but [is going away sooner than later](https://github.com/ionide/ionide-vscode-fsharp/wiki/Version-3.13.0#experimental---use-net-core-fsautocomplete)

[http://www.mono-project.com/download/](http://www.mono-project.com/download/)

Recommended latest stable 5.4 (or 5.2), required >= 4.8

the package is the `mono-complete` (who already contains the `mono-devel`)

Check if is installed correctly with `mono --version` should print:

```
Mono JIT compiler version 5.4.1.6 (tarball Wed Nov  8 20:37:08 UTC 2017)
```

<a name="vscode"></a>
## Visual Studio Code

Install:

[https://code.visualstudio.com/download](https://code.visualstudio.com/download)

Extensions to install ([docs](https://code.visualstudio.com/docs/editor/extension-gallery)):

- [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
- [Ionide-fsharp](https://marketplace.visualstudio.com/items?itemName=Ionide.Ionide-fsharp)

After first install, you should download also the .net core debugger.

- open VS Code
- in the `Command Palette` run `> Debug: Download .NET Core Debugger`

![command]({{ site.baseurl }}/assets/command_to_install_debugger.png)

- this will focus the `C#` panel below, and start the download and install of the debugger

![installed]({{ site.baseurl }}/assets/installing_debugger_finished.png)

<a name="docker"></a>
## Docker images

Docker 17.06 or higher is recommended because the workshop use the [multi stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/), but can be adapted if a previous versions.

Docker images to download:

```bash
docker pull microsoft/dotnet:2-sdk
docker pull microsoft/dotnet:2-runtime
docker pull microsoft/dotnet:2-runtime-deps
```

Check is installed correctly:

```bash
docker run --rm microsoft/dotnet:2-sdk dotnet --info
```

should print:

```
.NET Command Line Tools (2.1.101)

Product Information:
 Version:            2.1.101
 Commit SHA-1 hash:  6c22303bf0

Runtime Environment:
 OS Name:     debian
 OS Version:  9
 OS Platform: Linux
 RID:         linux-x64
 Base Path:   /usr/share/dotnet/sdk/2.1.101/

Microsoft .NET Core Shared Framework Host

  Version  : 2.0.6
  Build    : 74b1c703813c8910df5b96f304b0f2b78cdf194d
```

and

```bash
docker run --rm microsoft/dotnet:2-runtime dotnet --info
```

should print:

```
Microsoft .NET Core Shared Framework Host

  Version  : 2.0.6
  Build    : 74b1c703813c8910df5b96f304b0f2b78cdf194d
```
