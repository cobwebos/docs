---
title: 使用自定义 NuGet 源
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: 使用自定义 NuGet 源访问和使用 Azure Dev Space 中的 NuGet 包。
keywords: Docker, Kubernetes, Azure, AKS, Azure 容器服务, 容器
manager: gwallace
ms.openlocfilehash: 39984a3b3a1be64a497fb8088559ccfcdee4f1c6
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325725"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Use a custom NuGet feed with Azure Dev Spaces

NuGet 源提供了一种方便方式将包源包含在项目中。 Azure Dev Spaces needs to access this feed in order for dependencies to be properly installed in the Docker container.

## <a name="set-up-a-nuget-feed"></a>设置 NuGet 源

Add a [package reference](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) for your dependency in the `*.csproj` file under the `PackageReference` node. 例如：

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Create a [NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file) file in the project folder and set the `packageSources` and `packageSourceCredentials` sections for your NuGet feed. The `packageSources` section contains your feed url, which must be accessible from your AKS cluster. The `packageSourceCredentials` are the credentials for accessing the feed. 例如：

```xml
<packageSources>
    <add key="Contoso" value="https://contoso.com/packages/" />
</packageSources>

<packageSourceCredentials>
    <Contoso>
        <add key="Username" value="user@contoso.com" />
        <add key="ClearTextPassword" value="33f!!lloppa" />
    </Contoso>
</packageSourceCredentials>
```

Update your Dockerfiles to copy the `NuGet.Config` file to the image. 例如：

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> On Windows, `NuGet.Config`, `Nuget.Config`, and `nuget.config` all works as valid file names. On Linux, only `NuGet.Config` is a valid file name for this file. Since Azure Dev Spaces uses Docker and Linux, this file must be named `NuGet.Config`. You can fix the naming manually or by running `dotnet restore --configfile nuget.config`.


If you are using Git, you should not have the credentials for your NuGet feed in version control. Add `NuGet.Config` to the `.gitignore` for your project so that the `NuGet.Config` file is not added to version control. Azure Dev Spaces will needs this file during the container image build process, but by default, it respects the rules defined in `.gitignore` and `.dockerignore` during synchronization. To change the default and allow Azure Dev Spaces to synchronize the `NuGet.Config` file, update the `azds.yaml` file:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

If you are not using Git, you can skip this step.

The next time you run `azds up` or hit `F5` in Visual Studio Code or Visual Studio, Azure Dev Spaces will synchronize the `NuGet.Config` file use it to install package dependencies.

## <a name="next-steps"></a>后续步骤

Learn more about [NuGet and how it works](https://docs.microsoft.com/nuget/what-is-nuget).