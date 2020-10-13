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
ms.openlocfilehash: d60d7142d9b9979be76eebb3d324a448bd76638f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91960212"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>使用带有 Azure Dev Spaces 的自定义 NuGet 源

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

NuGet 源提供了一种方便方式将包源包含在项目中。 Azure Dev Spaces 需要访问此源，以便在 Docker 容器中正确安装依赖项。

## <a name="set-up-a-nuget-feed"></a>设置 NuGet 源

将依赖项的 [包引用](/nuget/consume-packages/package-references-in-project-files) 添加到 " `*.csproj` " 节点下的文件中 `PackageReference` 。 例如：

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

在项目文件夹中创建 [NuGet.Config](/nuget/reference/nuget-config-file) 文件，并 `packageSources` `packageSourceCredentials` 为 NuGet 源设置和部分。 `packageSources`部分包含源 url，必须可从 AKS 群集访问该 url。 `packageSourceCredentials`是用于访问源的凭据。 例如：

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

更新 Dockerfile，将文件复制 `NuGet.Config` 到映像。 例如：

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> 在 Windows 上，、 `NuGet.Config` `Nuget.Config` 和 `nuget.config` 都可以作为有效的文件名。 在 Linux 上，只 `NuGet.Config` 是此文件的有效文件名。 由于 Azure Dev Spaces 使用 Docker 和 Linux，因此必须将此文件命名为 `NuGet.Config` 。 可以手动或通过运行来修复命名 `dotnet restore --configfile nuget.config` 。


如果使用的是 Git，则不应在版本控制中具有 NuGet 源的凭据。 将添加 `NuGet.Config` 到 `.gitignore` 项目的，以便不将该 `NuGet.Config` 文件添加到版本控制中。 Azure Dev Spaces 将在容器映像生成过程中需要此文件，但默认情况下，它会考虑在 `.gitignore` 同步期间和中定义的规则 `.dockerignore` 。 若要更改默认值并允许 Azure Dev Spaces 同步 `NuGet.Config` 文件，请更新 `azds.yaml` 文件：

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

如果未使用 Git，则可以跳过此步骤。

下一次 `azds up` `F5` 在 Visual Studio Code 或 Visual Studio 中运行或命中时，Azure Dev Spaces 会同步 `NuGet.Config` 文件，使用它来安装包依赖项。

## <a name="next-steps"></a>后续步骤

详细了解 [NuGet 及其工作原理](/nuget/what-is-nuget)。
