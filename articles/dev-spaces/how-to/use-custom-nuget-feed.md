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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325725"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>将自定义 NuGet 源与 Azure 开发空间一起使用

NuGet 源提供了一种方便方式将包源包含在项目中。 Azure 开发人员空间需要访问此源，以便在 Docker 容器中正确安装依赖项。

## <a name="set-up-a-nuget-feed"></a>设置 NuGet 源

`*.csproj`在`PackageReference`节点下的文件中为依赖项添加[包引用](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files)。 例如：

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

在项目文件夹中创建[NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file)文件，并为`packageSources`NuGet 源设置 和`packageSourceCredentials`节。 该`packageSources`部分包含源 URL，必须从 AKS 群集访问该 URL。 是`packageSourceCredentials`访问源的凭据。 例如：

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

更新 Docker 文件以将`NuGet.Config`文件复制到映像。 例如：

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> 在 Windows `NuGet.Config``Nuget.Config`上，`nuget.config`和 和所有都用作有效的文件名。 在 Linux`NuGet.Config`上，仅此文件的有效文件名。 由于 Azure 开发人员空间使用 Docker 和 Linux，`NuGet.Config`因此必须命名此文件。 您可以手动修复命名，也可以通过运行`dotnet restore --configfile nuget.config`来修复命名。


如果使用 Git，则不应在版本控件中具有 NuGet 源的凭据。 添加到`NuGet.Config`项目的`.gitignore`， 以便`NuGet.Config`文件不会添加到版本控件。 Azure 开发人员空间在容器映像生成过程中需要此文件，但默认情况下，它尊重同步期间`.gitignore`和`.dockerignore`同步期间定义的规则。 要更改默认值并允许 Azure 开发空间同步文件，`NuGet.Config`请`azds.yaml`更新该文件：

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

如果您不使用 Git，则可以跳过此步骤。

下次在 Visual `azds up` Studio`F5`代码或可视化工作室中运行或命中时，Azure 开发人员空间`NuGet.Config`将同步文件使用它安装包依赖项。

## <a name="next-steps"></a>后续步骤

了解更多关于[NuGet及其工作原理](https://docs.microsoft.com/nuget/what-is-nuget)。