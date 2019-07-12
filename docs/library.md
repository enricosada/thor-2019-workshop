---
permalink: /library/
---

# library

In a `sample2` directory, run

```
mkdir sample2 && cd sample2
```

## dotnet new

Now let's create a library app, run

```
dotnet new lib -n l1 -lang f#
```

the `-n` pass the name of the project, and create a subdirectory.

and open 

```
code .
```

Because the project is in a subdirectory, the `dotnet build` fails because doesnt know
what project to build. Is enough to pass the directory or the path of project file.

```
dotnet build l1
```

Same in code, in `tasks.json`, set `"command": "dotnet build l1",`

## reference from a console app

as before, create a simple console app `c1`

```
dotnet new console -n c1 -lang f#
```

And now reference the `l1` from `c1`

```
dotnet add c1 reference l1
```

Is now possibile to use the methods from l1 in c1
Add to main

```fsharp
    l1.Say.hello "Thor!"
```

to run it:

- or `dotnet run -p c1` from terminal
- or update the `tasks.json` and `launch.json` to correct c1 paths, for code

this is neeeded because `dotnet build` doesnt build all project in the subdirectories, but
all project referenced from the initial one.

## test with xunit

To add an unit test project, based on [xUnit](https://xunit.github.io/) Library

```
dotnet new xunit -n l1.Tests -lang f#
```

And add the project reference to `l1`

```
dotnet add l1.Tests reference l1/l1.fsproj
```

Now let's change the l1 to refactor and extract an `helloMessage` function

```fsharp
    let helloMessage name =
        sprintf "Hello %s" name

    let hello name =
        printfn "%s" (helloMessage name)
```

and test it in `Tests.fs`

```fsharp
[<Fact>]
let ``hello`` () =
    Assert.Equal("Hello Thor", (l1.Say.helloMessage "Thor at JET"))
```

the test can be run with `dotnet test l1.Tests`


In vscode, configure the test task in `tasks.json`

- same as build task, but with:
  - group kind `test` instead of `build`
  - command: `dotnet test l1.Tests`

Can be set as default with `> Tasks: Configure Default Test Task`

Now tests can be executed with

- `> Tasks: Run Test Task`

## watch

You can also use `dotnet watch` to watch for changes in files and rerun any command

For example for tests

```
dotnet watch -p l1.Tests test
```

## test with nunit

The `dotnet new` allow to import additinal templates.
for example, let use nunit

First let's import the template

```
dotnet new -i "NUnit3.DotNetNew.Template::*"
```

now the nunit template is installed (see `dotnet new --list`)

```
Templates                         Short Name       Language          Tags
-------------------------------------------------------------------------
NUnit 3 Test Project              nunit            [C#], F#, VB      Test/NUnit
```

to create a new project (`-o` is needed to specify output path)

```
dotnet new nunit -n l1.Tests2 -lang f# -f netcoreapp2.1
```

And add the project reference to `l1`

```
dotnet add l1.Tests2 reference l1
```

and add a new test case

```fsharp
    [<Test>]
    member this.HelloMessage () =
        Assert.AreEqual("Hello Thor at JET", l1.Say.helloMessage "Thor")
```

and run it with `dotnet test l1.Tests2`.

## pack

To create the nuget package from library, is possibile to use the `dotnet pack` command

```
dotnet pack l1 -c Release /p:Version=1.2.3
```

this will create a package

```
  Successfully created package 'C:\thor-w\sample2\l1\bin\Release\l1.1.2.3.nupkg'.
```

To specify other package metadata (like `Version`), is possibile to:

- pass it as command line, like `/p:Version=1.2.3`
- pass is as environment variable (so `Version`)
- or specify it in an msbuild property inside the like `<Version>1.2.3</Version>`

Others `Authors`, `Summary`, `Description`, `PackageTags`, etc.
See [nuspec metadata as properties docs](https://docs.microsoft.com/en-us/dotnet/core/tools/csproj#nuget-metadata-properties)  for more info

## push

to push a package to a nuget feed, you can use `dotnet nuget push`

For example, to push to myget feed

RUN

```
dotnet nuget push l1/bin/Release/l1.1.2.3.nupkg -s https://www.myget.org/F/thor-workshop/api/v2/package -k fb4e7360-9c89-4a25-982f-64760ca089a5
```

to restore from a dev feed:

```
dotnet add package l1 --version 1.2.3 --source https://www.myget.org/F/thor-workshop/api/v3/index.json
```


Custom feed can be set with msbuild property `RestoreAdditionalSources`

Additionally, you can manage feeds with a `nuget.config` file.
You can create it with `dotnet new nugetconfig`
