---
title: 创建 a.Net Core 应用并将其发布到远程 Linux 群集
description: 从 Visual Studio 创建和发布面向远程 Linux 群集 .Net Core 应用
author: peterpogorski
ms.topic: troubleshooting
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: c30eedb6782e4172d677f16e27441f28c78cdd89
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614343"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>使用 Visual Studio 创建和发布面向远程 Linux Service Fabric 群集的 .Net Core 应用程序
使用 Visual Studio 工具，你可以开发和发布面向 Linux Service Fabric 群集的 Service Fabric .Net Core 应用程序。 SDK 版本必须为3.4 或更高版本，才能从 Visual Studio 部署面向 Linux Service Fabric 群集的 .Net Core 应用程序。

> [!Note]
> Visual Studio 不支持对面向 Linux Service Fabric 应用程序进行调试。
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>创建 Service Fabric 应用程序目标 .Net Core
1. 以管理员身份启动 Visual Studio。
2. 使用**文件 > > 项目**创建项目。
3. 在 "**新建项目**" 对话框中，选择 "**云 > Service Fabric 应用程序**"。
![create-application]
4. 为应用程序命名，然后单击 **"确定"** 。
5. 在 "**新建 Service Fabric 服务**" 页上，选择要在 **.Net Core 部分**下创建的服务的类型。
![create-service]

## <a name="deploy-to-a-remote-linux-cluster"></a>部署到远程 Linux 群集
1. 在 "解决方案资源管理器" 中，右键单击应用程序并选择 "**生成**"。
![build-application]
2. 完成应用程序的生成过程后，右键单击该服务，然后选择 "编辑 **.csproj 文件**"。
![edit-csproj]
3. 如果服务是执行组件**项目类型**，则将 UpdateServiceFabricManifestEnabled 属性从 True 编辑为**False** 。 如果你的应用程序没有执行组件服务，请跳到步骤4。
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> 如果将 UpdateServiceFabricManifestEnabled 设置为 false，则在生成过程中将禁用对 Servicemanifest.xml 的更新。 对服务进行的任何更改（如添加、删除或重命名）都不会反映在 Servicemanifest.xml 中。 如果进行了任何更改，则必须手动更新 Servicemanifest.xml，或者暂时将 UpdateServiceFabricManifestEnabled 设置为 true，并生成将更新 Servicemanifest.xml 的服务，然后将其恢复回 false。
>

4. 将 RuntimeIndetifier 从 win7-x64 更新到服务项目中的目标平台。
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. 在 Servicemanifest.xml 中，更新 entrypoint 程序以删除 .exe。 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. 在解决方案资源管理器中，右键单击应用程序，然后选择 "**发布**"。 此时会显示“发布”对话框。
7. 在 "**连接终结点**" 中，选择要作为目标的远程 Service Fabric Linux 群集的终结点。
![publish-application]

<!--Image references-->
[create-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[create-service]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[build-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[publish-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>后续步骤
* 了解[Service Fabric 与 .Net Core](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)入门
