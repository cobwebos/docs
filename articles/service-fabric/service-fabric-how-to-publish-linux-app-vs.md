---
title: 创建a.Net核心应用并将其发布到远程 Linux 群集
description: 创建和发布 .Net Core 应用程序，从 Visual Studio 创建远程 Linux 群集
author: peterpogorski
ms.topic: troubleshooting
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: c30eedb6782e4172d677f16e27441f28c78cdd89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614343"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>使用 Visual Studio 创建和发布面向远程 Linux 服务交换矩阵群集的 .Net Core 应用程序
借助 Visual Studio 工具，您可以开发和发布面向 Linux 服务交换矩阵群集的服务交换矩阵 .Net Core 应用程序。 SDK 版本必须为 3.4 或以上，才能从 Visual Studio 部署针对 Linux 服务交换矩阵群集的 .Net Core 应用程序。

> [!Note]
> Visual Studio 不支持调试面向 Linux 的 Service Fabric 应用程序。
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>创建面向 .Net 核心的服务交换矩阵应用程序
1. 以管理员身份启动 Visual Studio。****
2. 使用**文件>"新>项目创建项目**"。
3. 在 **"新项目**"对话框中，选择**云->服务结构应用程序**。
![create-application]
4. 命名应用程序，然后单击 **"确定**"。
5. 在“新建 Service Fabric 服务”页中，选择要在“.NET Core 部分”创建的服务类型。********
![create-service]

## <a name="deploy-to-a-remote-linux-cluster"></a>部署到远程 Linux 群集
1. 在“解决方案资源管理器”中，右键单击该应用程序并选择“生成”。****
![build-application]
2. 应用程序的生成过程完成后，右键单击服务，然后选择编辑 **csproj 文件**。
![edit-csproj]
3. 将 UpdateServiceFabricManifestEnabled 属性从 True 编辑为 **False**，前提是服务为**执行组件项目类型**。 如果应用程序没有执行组件服务，请跳到步骤 4。
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> 将 UpdateServiceFabricManifestEnabled 设置为 false 会禁止在生成过程中更新 ServiceManifest.xml。 任何更改（例如添加、删除或重命名此服务）都不会反映在 ServiceManifest.xml 中。 如果进行了任何更改，则必须手动更新 ServiceManifest manually，或者将 UpdateServiceFabricManifestEnabled 临时设置为 true 并生成一个服务来更新 ServiceManifest.xml，然后将其恢复为 false。
>

4. 在服务项目中将 RuntimeIndetifier 从 win7-x64 更新为目标平台。
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. 在 ServiceManifest 中更新入口点程序，删除 .exe。 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. 在“解决方案资源管理器”中，右键单击该应用程序并选择“发布”。**** 此时会显示“发布”对话框。****
7. 在“连接终结点”中，选择要将其作为目标的远程 Service Fabric Linux 群集的终结点****。
![publish-application]

<!--Image references-->
[创建应用程序]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[创建服务]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[build-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[publish-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>后续步骤
* 了解[使用 .Net 核心开始使用服务交换矩阵](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
