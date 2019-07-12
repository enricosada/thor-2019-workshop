---
permalink: /docker/
---

Based on docs from https://github.com/dotnet/dotnet-docker-samples

Three workflow:

1. use `sdk` docker image, build and run inside container
2. use `sdk` docker image to build, and use `runtime` image to run it
3. self contained, with any image based on os (supported by .net core).

## console app

Use workflow 2, build with sdk and run with runtime image

This is supported by multi staged docker build

first, let's create a console app in a `sample4` directory, run:

```
dotnet new console -lang f# -o sample4
```

Now create a `Dockerfile`:

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:2.1.701 AS build-env
WORKDIR /app

# copy fsproj and restore as distinct layers
COPY *.fsproj ./
RUN dotnet restore

# copy everything else and build
COPY . ./
RUN dotnet publish -c Release -o out

# build runtime image
FROM mcr.microsoft.com/dotnet/core/runtime:2.1 
WORKDIR /app
COPY --from=build-env /app/out ./
ENTRYPOINT ["dotnet", "sample4.dll"]
```

The `COPY` will copy all files, so let's exclude temp dir who can exists locally

Add a `.dockerignore` file with

```
bin/
obj/
out/
```

and build it

```
docker build -t sample4 .
```

and run it

```
docker run --rm sample4 "from inside docker"
```

# web app

Let's use suave, with workflow 3

first, let's create a console app in a `sample5` directory

```
dotnet new console -lang f# -n sample5
```

And add Suave

```
dotnet add package Suave
```

Now add suave code in `main`

with an

```fsharp
open Suave
```

and

```fsharp
    startWebServer
        { defaultConfig with
            bindings = [ HttpBinding.create HTTP System.Net.IPAddress.Any 8083us ] }
        (Successful.OK "Hello World!")
```

we can try locally with `dotnet run`, who will create a server listening to 0.0.0.0:8083
and that's exposed to host at http://127.0.0.1:8083

Now the `Dockerfile`

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:2.1.701 AS build-env
WORKDIR /app

# copy fsproj and restore as distinct layers
# COPY nuget.config ./
COPY *.fsproj ./
RUN dotnet restore

# copy everything else and build
COPY . ./
RUN dotnet publish -c Release -r linux-x64 -o out

# build runtime image
FROM mcr.microsoft.com/dotnet/core/runtime-deps:2.1
WORKDIR /app
COPY --from=build-env /app/out ./
ENTRYPOINT ["./sample5"]
```

And the `.dockerignore`

```
bin/
obj/
out/
```

Now build

```
docker build -t sample5 .
```

and run

```
docker run --rm -p 8083:8083 sample5
```

## debug containers

first, let's create a console app in a `sample6` directory

```
dotnet new console -lang f# -n sample6
```

add some code to read current working directory, and wait for input (so process doesnt exit)

```fsharp
    let dir = System.Environment.CurrentDirectory
    printfn "workingDir '%s'" dir

    let x = Console.ReadLine()
    printfn "echo %s" x
```

and a `Dockerfile` to build it

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:2.1.701 AS build-env

# install debugger
RUN apt update && \
    apt install unzip && \
    curl -sSL https://aka.ms/getvsdbgsh | /bin/sh /dev/stdin -v latest -l /vsdbg

# copy everything else and build
WORKDIR /app

COPY . ./
RUN dotnet publish -o out

ENTRYPOINT ["dotnet", "./out/sample6.dll"]
```

And the usual `.dockerignore`

```
bin/
obj/
out/
```

We need now to add in the `launch.json` a configuration to attach to remote docker process.

`NOTE` the `admiring_beaver` is the name of the container
`NOTE` the `/app` is the working dir of the container, to map the same path for sources


```json
        {
            "name": ".NET Core Docker Attach",
            "type": "coreclr",
            "request": "attach",
            "processId": "${command:pickRemoteProcess}",
            "pipeTransport": {
                "pipeProgram": "docker",
                "pipeArgs": [ "exec", "-i", "admiring_beaver" ],
                "debuggerPath": "/vsdbg/vsdbg",
                "pipeCwd": "${workspaceRoot}",
                "quoteArgs": false
            },
            "sourceFileMap": {
                "/app": "${workspaceRoot}"
            }
        }
```

And build the container and run it

```
docker build -t sample6 .
docker run --name admiring_beaver --rm -i -t sample6
```

the container will wait for input, we can now attach to it.

Start debug of `.NET Core Docker Attach` and choose the `dotnet` process

![choose process]({{ site.baseurl }}/assets/attach_debug_docker.png)

Now you can paused, and you are in debug, with breakpoints etc

