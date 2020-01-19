# DocFX for Unity

> [DocFX](https://dotnet.github.io/docfx/index.html) usage example for Unity projects (Unity API xref map included)

[![Build status](https://ci.appveyor.com/api/projects/status/00mejohk0tfxqy7x/branch/master?svg=true)](https://ci.appveyor.com/project/NormandErwan/docfxforunity/branch/master) Example documentation website

[![Build status](https://ci.appveyor.com/api/projects/status/00mejohk0tfxqy7x/branch/UnityXrefMaps?svg=true)](https://ci.appveyor.com/project/NormandErwan/docfxforunity/branch/UnityXrefMaps) Unity API xref maps

[DocFX](https://dotnet.github.io/docfx/) tool generates a clean documentation that looks like the
[Unity documentation](https://docs.unity3d.com/Manual/index.html) with a manual (written in Markdown) and a scripting
API (from the C# scripts of the project).

This repository contains:

1. A simple Unity project as example. Its documentation is automatically generated and deployed
online with every `git push`: <https://normanderwan.github.io/DocFxForUnity/>.
2. Every reference to the C# API and the Unity API (the so-called
[xref maps](https://dotnet.github.io/docfx/tutorial/links_and_cross_references.html#cross-reference-between-projects)).
They are be automatically linked on the documentation.

[![DocFxForUnity documentation manual](https://normanderwan.github.io/DocFxForUnity/resources/ExampleManual.png)](https://normanderwan.github.io/DocFxForUnity/manual/coniunctis.html)

*DocFxForUnity documentation manual*

[![DocFxForUnity documentation scripting API](https://normanderwan.github.io/DocFxForUnity/resources/ExampleScriptingApi.png)](https://normanderwan.github.io/DocFxForUnity/api/DocFxForUnity.Player.html)

*DocFxForUnity documentation scripting API*

## Setup your documentation

1. [Install DocFX](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool).
2. Copy the `Documentation/` folder to your Unity project:

    ```bash
    .
    ├── Assets
    ├── Documentation # Copy it at the root of your project
    ├── Package
    ├── ProjectSettings
    └── README.md
    ```

3. Edit the following properties in `Documentation/docfx.json`, keep the others as it is:

    ```javascript
      {
        "build": {
          "globalMetadata": // Edit your documentation website info, see: https://dotnet.github.io/docfx/tutorial/docfx.exe_user_manual.html#322-reserved-metadata
          {
            "_appTitle": "Example Unity documentation",
            "_appFooter": "Example Unity documentation",
            "_enableSearch": true
          },
          "sitemap":
          {
            "baseUrl": "https://normanderwan.github.io/DocFxForUnity" // The URL of your documentation website
          }
      }
    ```

    It's the configuration file of your documentation.
    See <https://dotnet.github.io/docfx/tutorial/docfx.exe_user_manual.html#3-docfxjson-format> for more details.

4. Edit `Documentation/filterConfig.yml`:

    ```yaml
    apiRules:
    - include: # The namespaces to generate
        uidRegex: ^Your\.Namespace1
        type: Namespace
    - include:
        uidRegex: ^Your\.Namespace2
        type: Namespace
    - exclude:
        uidRegex: .* # Every other namespaces are ignored
        type: Namespace
    ```

    It tells DocFX which namespaces you want to generate the documentation.
    See <https://dotnet.github.io/docfx/tutorial/howto_filter_out_unwanted_apis_attributes.html> for more details.

5. Write your manual pages in Markdown in `Documentation/manual/`.
You must keep a list of these pages on `Documentation/manual/toc.yml`.
6. Add resources such as images to `Documentation/resources/`.
7. Generate your documentation:
    - On a command line opened on your project, run:

        ```bash
        cp README.md Documentation/index.md
        docfx Documentation/docfx.json --serve
        ```

    - The generated website will be visible at <http://localhost:8080>.

## Generate automatically your documentation

If you're using GitLab, use the provide
[`.gitlab-ci.yml`](https://github.com/NormandErwan/DocFxForUnity/blob/master/.gitlab-ci.yml).
Generated website is pushed to a `public/` directory. See the
[GitLab Pages documentation](https://docs.gitlab.com/ee/user/project/pages/getting_started_part_four.html) for more
details.

## Troubleshooting / FAQ

- DocFX outputs: `Warning:[ExtractMetadata]No project detected for extracting metadata.`

    Solution: On Unity, click [Asset > Open C# Project](https://docs.microsoft.com/fr-fr/visualstudio/cross-platform/media/vstu_open-csharp-project.png?view=vs-2019) to generate the required `.csproj`.

- DocFX outputs: `Warning:[ExtractMetadata]No metadata is generated for Assembly-CSharp,Assembly-CSharp-Editor.`

    Solutions:

    1. Make sure your included your namespace in `Documentation/filterConfig.yml`:

        ```yaml
        - include:
          uidRegex: ^Your\.Namespace1
          type: Namespace
        ```

    2. If you don't want to use a namespace, remove this line on `Documentation/docfx.json`:

        ```diff
        {
            "metadata": [
                {
                    ...
        -           "filter": "filterConfig.yml",
                    "dest": "api/"
                }
            ],
            ...
        }
        ```