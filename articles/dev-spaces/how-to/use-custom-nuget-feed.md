---
title: 如何在 Azure Dev Spaces 中使用自定义 NuGet 源
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: 使用自定义 NuGet 源访问和使用 Azure Dev Space 中的 NuGet 包。
keywords: Docker, Kubernetes, Azure, AKS, Azure 容器服务, 容器
manager: gwallace
ms.openlocfilehash: 9df095011c1ff66ff0c85993c7c85dffe62623b8
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873255"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>在 Azure Dev Space 中使用自定义 NuGet 源

NuGet 源提供了一种方便方式将包源包含在项目中。 Azure Dev Spaces 需要访问此源, 以便在 Docker 容器中正确安装依赖项。

## <a name="set-up-a-nuget-feed"></a>设置 NuGet 源

将依赖项的[包引用](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files)添加到 " `*.csproj` " `PackageReference`节点下的文件中。 例如：

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

在项目文件夹中创建一个[nuget .config](https://docs.microsoft.com/nuget/reference/nuget-config-file)文件, 并为 NuGet `packageSources`源`packageSourceCredentials`设置和节。 `packageSources`部分包含源 url，必须可从 AKS 群集访问该 url。 `packageSourceCredentials`是用于访问源的凭据。 例如：

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

更新 dockerfile, 将`NuGet.Config`文件复制到映像。 例如：

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> 在 Windows 上`NuGet.Config`, `Nuget.Config`、和`nuget.config`都可以作为有效的文件名。 在 Linux 上, `NuGet.Config`只是此文件的有效文件名。 由于 Azure Dev Spaces 使用 Docker 和 Linux, 因此必须将此文件`NuGet.Config`命名为。 可以手动或通过运行`dotnet restore --configfile nuget.config`来修复命名。


如果使用的是 Git, 则不应在版本控制中具有 NuGet 源的凭据。 将`NuGet.Config`添加到`.gitignore`项目的, 以便不将`NuGet.Config`该文件添加到版本控制中。 Azure Dev Spaces 将在容器映像生成过程中需要此文件, 但默认情况下, 它会考虑在同步`.gitignore`期间`.dockerignore`和中定义的规则。 若要更改默认值并允许 Azure Dev Spaces 同步`NuGet.Config`文件, 请`azds.yaml`更新文件:

```yaml
build:
useGitIgnore: true
ignore:
- “!NuGet.Config”
```

如果未使用 Git, 则可以跳过此步骤。

下一次在 Visual Studio Code `azds up`或 Visual `F5` Studio 中运行或命中时`NuGet.Config` , Azure Dev Spaces 会同步文件, 使用它来安装包依赖项。

## <a name="next-steps"></a>后续步骤

详细了解[NuGet 及其工作原理](https://docs.microsoft.com/nuget/what-is-nuget)。