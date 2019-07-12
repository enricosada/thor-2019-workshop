---
permalink: /requirements/
---

# Requirements

- [.NET Core Sdk 2.1](#dotnetsdk)
- [Mono](#mono) (on unix/mac)
- An editor:
  - [Visual Studio Code](#vscode)
    - C# extension
    - Ionide-fsharp extension
- [Docker and images](#docker) (optional)

Optional editors:

- Visual Studio 2017 [install info](https://docs.microsoft.com/en-us/dotnet/fsharp/get-started/get-started-visual-studio#installing-f) ( version >= 15.6 )
- JetBrains Rider [install info](https://www.jetbrains.com/rider/)


Any v2.1.x (sdk, runtime, docker, etc) is ok. Instructions show latest avaiable.

<a name="dotnetsdk"></a>
## .NET Core Sdk 2.1

Install the Sdk (not the Runtime):

[https://dotnet.microsoft.com/download/dotnet-core](https://dotnet.microsoft.com/download/dotnet-core)


- Choose the version [.NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1)
- In the `Sdk` column, download the `.NET Core Installer` (x64 is ok)
- The latest version currently (and used in this workshop) is `2.1.701`

![project explorer]({{ site.baseurl }}/assets/install_netcoresdk.png)

Prerequisites:

- [linux](https://docs.microsoft.com/en-us/dotnet/core/linux-prerequisites?tabs=netcore2x)
- [osx](https://docs.microsoft.com/en-us/dotnet/core/macos-prerequisites)
- [windows](https://docs.microsoft.com/en-us/dotnet/core/windows-prerequisites?tabs=netcore2x)

Check if is installed correctly with `dotnet --info` should print:

```
.NET Core SDK (reflecting any global.json):
 Version:   2.1.701
 Commit:    8cf7278aa1

<additional info about os>
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
docker pull mcr.microsoft.com/dotnet/core/sdk:2.1.701
docker pull mcr.microsoft.com/dotnet/core/runtime:2.1
docker pull mcr.microsoft.com/dotnet/core/runtime-deps:2.1
```

Check is installed correctly:

```bash
docker run --rm mcr.microsoft.com/dotnet/core/sdk:2.1.701 dotnet --version
```

should print:

```
.NET Core SDK (reflecting any global.json):
 Version:   2.1.701
 Commit:    8cf7278aa1

Runtime Environment:
 OS Name:     debian
 OS Version:  9
 OS Platform: Linux
 RID:         debian.9-x64
 Base Path:   /usr/share/dotnet/sdk/2.1.701/

Host (useful for support):
  Version: 2.1.12
  Commit:  ccea2e606d

.NET Core SDKs installed:
  2.1.701 [/usr/share/dotnet/sdk]

.NET Core runtimes installed:
  Microsoft.AspNetCore.All 2.1.12 [/usr/share/dotnet/shared/Microsoft.AspNetCore.All]
  Microsoft.AspNetCore.App 2.1.12 [/usr/share/dotnet/shared/Microsoft.AspNetCore.App]
  Microsoft.NETCore.App 2.1.12 [/usr/share/dotnet/shared/Microsoft.NETCore.App]
```

and

```bash
docker run --rm mcr.microsoft.com/dotnet/core/runtime:2.1 dotnet --info
```

should print:

```
Host (useful for support):
  Version: 2.1.12
  Commit:  ccea2e606d

.NET Core SDKs installed:
  No SDKs were found.

.NET Core runtimes installed:
  Microsoft.NETCore.App 2.1.12 [/usr/share/dotnet/shared/Microsoft.NETCore.App]
```
