---
title: 如何在 Azure Dev Spaces 中使用自定义 NuGet 源 | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: johnsta
ms.author: johnsta
ms.date: 05/11/2018
ms.topic: article
description: 使用自定义 NuGet 源访问和使用 Azure Dev Space 中的 NuGet 包。
keywords: Docker, Kubernetes, Azure, AKS, Azure 容器服务, 容器
manager: ghogen
ms.openlocfilehash: 3badd15bcfd09c97b43744a20c5df05f4ff57e84
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199103"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>在 Azure Dev Space 中使用自定义 NuGet 源

NuGet 源提供了一种方便方式将包源包含在项目中。 Azure Dev Spaces 需要能够访问此源，以便将依赖项正确安装到 Docker 容器中。

## <a name="set-up-a-nuget-feed"></a>设置 NuGet 源

若要设置 NuGet 源，请执行以下操作：
1. 在 `PackageReference` 节点下的 `*.csproj` 文件中添加[包引用](https://docs.microsoft.com/en-us/nuget/consume-packages/package-references-in-project-files)。

   ```xml
   <ItemGroup>
       <!-- ... -->
       <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
       <!-- ... -->
   </ItemGroup>
   ```

2. 在项目文件夹中创建一个 [NuGet.Config](https://docs.microsoft.com/en-us/nuget/reference/nuget-config-file) 文件。
     * 使用 `packageSources` 节引用 NuGet 源位置。 重要说明：NuGet 源必须可公开访问。
     * 使用 `packageSourceCredentials` 节配置用户名和密码凭据。 

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

3. 如果使用源代码管理：
    - 在 `.gitignore` 文件中引用 `NuGet.Config`，这样就不会意外地将凭据提交到源存储库。
    - 打开项目中的 `azds.yaml` 文件，找到 `build` 节，然后插入以下代码片段，以确保将 `NuGet.Config` 文件同步到 Azure，以便在容器映像生成过程中使用它。 （默认情况下，Azure Dev Spaces 不会同步与 `.gitignore` 和 `.dockerignore` 规则匹配的文件。）

        ```yaml
        build:
        useGitIgnore: true
        ignore:
        - “!NuGet.Config”
        ```


## <a name="next-steps"></a>后续步骤

完成上述步骤后，下次运行 `azds up`（或者在 VSCode 或 Visual Studio 中点击 `F5`）时，Azure Dev Spaces 会将 `NuGet.Config` 文件同步到 Azure，然后 `dotnet restore` 会利用该文件在容器中安装包依赖项。

