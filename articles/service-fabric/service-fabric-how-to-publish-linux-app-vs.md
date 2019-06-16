---
title: 了解如何创建和发布.Net Core 应用程序与远程 Azure Service Fabric Linux 群集 |Microsoft Docs
description: 创建和发布.Net Core 面向 Visual Studio 中的远程 Linux 群集的应用程序
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: 46d76edbe8cede12e8c7811f43c28a65c1ebaed0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078659"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>使用 Visual Studio 创建和发布.Net Core 面向远程 Linux Service Fabric 群集的应用程序
使用 Visual Studio 工具，可以开发和发布 Service Fabric.Net Core 面向 Linux Service Fabric 群集的应用程序。 SDK 版本必须是 3.4 或更高版本来部署.Net Core 应用程序面向 Linux Service Fabric 群集从 Visual Studio。

> [!Note]
> Visual Studio 不支持针对 Linux 调试 Service Fabric 应用程序。
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>创建面向.Net Core 的 Service Fabric 应用程序
1. 以管理员身份启动 Visual Studio。 
2. 创建与项目**文件-> 新建-> 项目**。
3. 在中**新的项目**对话框中，选择**云-> Service Fabric 应用程序**。
![create-application]
4. 命名应用程序，然后单击**确定**。
5. 上**新的 Service Fabric 服务**页上，选择你想要在创建的服务的类型 **.Net Core 部分**。
![create-service]

## <a name="deploy-to-a-remote-linux-cluster"></a>将部署到远程 Linux 群集
1. 在解决方案资源管理器，右键单击该应用程序并选择**生成**。
![build-application]
2. 完成应用程序的生成过程后，在服务上右键单击，然后选择编辑**csproj 文件**。
![edit-csproj]
3. 编辑 UpdateServiceFabricManifestEnabled 属性从 True 到**False**服务是否**执行组件项目类型**。 如果你的应用程序不具有执行组件服务，请跳到步骤 4。
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> 将 UpdateServiceFabricManifestEnabled 设置为 false，将在生成期间禁用 ServiceManifest.xml 的更新。 任何此类的更改如添加、 删除或重命名为该服务将不会反映在 ServiceManifest.xml 中。 如果您进行任何更改必须的更新 ServiceManifest 手动或暂时设置 UpdateServiceFabricManifestEnabled 为 true，并生成服务，将更新 ServiceManifest.xml，然后将其恢复回 false。
>

4. 更新到服务项目中的目标平台从 win7-x64 RuntimeIndetifier。
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. ServiceManifest 中更新该入口点程序以删除.exe。 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. 在解决方案资源管理器中右键单击该应用程序并选择**发布**。 此时会显示“发布”对话框。 
7. 在中**连接终结点**，选择你想要面向的远程 Service Fabric Linux 群集的终结点。
![publish-application]

<!--Image references-->
[create-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[create-service]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[build-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[publish-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>后续步骤
* 了解有关[使用.Net Core 的 Service Fabric 入门](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
