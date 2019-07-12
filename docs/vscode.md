---
permalink: /vscode/
---

## First time install

You probably need the debugger.

```
> Debug: Download .NET Core debugger
```

**NOTE** That require the `C#` extension to be installed, see Requirements


## Issues with intellisense

For issue (red squiggles, references not works, etc), you can try

to restart

```
> Reload Window
```

To clear cache of project info

```
> F#: Clear cache
```

and to change the workspace/sln

```
> F#: Change Workspace or Solution
```

## Settings

can be changed for repo, workspace or user with

```
> Preferences: Open User Settings
```

Some nice editor improvements

```json
    // whitespace
    "editor.renderWhitespace": "boundary",

    // linelens instead of codelens
    "editor.codeLens": false,
    "FSharp.lineLens.enabled": "replaceCodeLens",
```

Disable defaults

```json
    "editor.minimap.enabled": false,
    "workbench.startupEditor": "none"
```

Enable ionide diagnostics

```json
    "FSharp.trace.server": "verbose"
```

## Remote development in container

more info in [https://code.visualstudio.com/docs/remote/containers](https://code.visualstudio.com/docs/remote/containers)

Create a `.devcontainer/Dockerfile` who contains the build environment

```
FROM mcr.microsoft.com/dotnet/core/sdk:2.2

# Configure apt
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update \
    && apt-get -y install --no-install-recommends apt-utils 2>&1

# Install git, process tools, lsb-release (common in install instructions for CLIs)
RUN apt-get -y install git procps lsb-release

# Clean up
RUN apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*
ENV DEBIAN_FRONTEND=dialog

# Set the default shell to bash rather than sh
ENV SHELL /bin/bash
```

Create the `.devcontainer/devcontainer.json` who contains the info about communication:

- `appPort` the port to expose
- `extensions` the extensions to install in the container (C# for debugger, ionide for F#)

```json
{
    "name": ".NET Core Sample",
    "dockerFile": "Dockerfile",
    "appPort": 8083,
    "extensions": [
        "ms-vscode.csharp",
        "ionide.ionide-fsharp"
    ]
}
```

Now reload `> Reload Window`

it will appear on the bottom righ a panel, to switch to remote development in the containers

![switch to remote]({{ site.baseurl }}/assets/code_remote_extension_start.png)

Choose `Reopen in container`, will reload the window, connnected to docker container as dev env

**NOTE** If the project loading fails at startup, run a build of the project from a `tasks.json` task.
That's becuase the project was restored on windows, so the `obj` dirs contains windows info

Now all command like build etc, will run on the container.

Debug too will work, as if is local

![switch to remote]({{ site.baseurl }}/assets/code_remote_extension_debug.png)



