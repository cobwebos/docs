---
title: 为 Service Fabric 网格应用设置 Windows 开发环境 | Microsoft Docs
description: 设置 Windows 开发环境，以便可以创建 Service Fabric 网格应用程序并将其部署到 Azure Service Fabric 网格。
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/20/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 589bef1894a3bee1e6974a0ea2516200fae2891f
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185537"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-applications"></a>设置 Windows 开发环境以生成 Service Fabric 应用程序

若要在 Windows 开发计算机上生成并运行 Azure Service Fabric 应用，请安装 Service Fabric 运行时、SDK 和工具。

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="supported-operating-system-versions"></a>支持的操作系统版本

支持使用以下操作系统版本进行开发：

* Windows 10（企业版、专业版或教育版）
* Windows Server 2016

## <a name="visual-studio"></a>Visual Studio

需有 Visual Studio 2017 才能部署 Service Fabric 应用。 [安装版本 15.6.0][download-visual-studio] 或更高版本，并启用以下工作负荷：

- ASP.NET 和 Web 开发
- Azure 开发
 
## <a name="windows-10---install-docker"></a>Windows 10 - 安装 Docker

下载并安装最新版本的 [Docker Community Edition for Windows][download-docker] 来支持 Service Fabric 网格使用的容器化 Service Fabric 应用。

在安装过程中出现提示时，请选择“使用 Windows 容器而不是 Linux 容器”。 如果你的计算机上未启用 Hyper-V，则 Docker 安装将请求启用它。 如果出现提示，单击“确定”以执行此操作。

## <a name="windows-server-2016---install-hyper-v-and-docker"></a>Windows Server 2016 - 安装 Hyper-V 和 Docker

**安装 Hyper-V**

首先，以管理员身份打开 PowerShell 并运行以下命令来安装 HYPER-V 并重启计算机。 有关详细信息，请参阅 [Docker Enterprise Edition for Windows Server][download-docker-server]。

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

**安装 Docker**

以管理员身份打开 PowerShell 并运行以下命令来安装 Docker：

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

重启计算机。

## <a name="sdk-and-tools"></a>SDK 和工具

按以下顺序安装 Service Fabric 网格运行时、SDK 和工具。

1. 使用 Web 平台安装程序安装 [Service Fabric 网格 SDK][download-sdkmesh]。 这也会安装 Microsoft Azure Service Fabric SDK 和运行时。
2. 从 Visual Studio 市场安装 [Visual Studio Service Fabric 工具（预览版）扩展][download-tools]。

## <a name="build-a-cluster"></a>生成群集

如果使用的是 Visual Studio，则可以跳过本部分，因为如果你还没有创建本地群集，Visual Studio 将创建它。

为了在创建和运行 Service Fabric 应用时获得最佳调试性能，我们建议创建单节点本地开发群集。 每当部署或调试 Service Fabric 网格项目时，都必须运行此群集。

在生成群集之前，Docker **必须**正在运行。 若要测试 Docker 是否正在运行，请打开一个终端窗口，运行 `docker ps` 并查看是否出错。 如果响应中未指示错误，则表示 Docker 正在运行，可以生成群集。

安装运行时、SDK 和 Visual Studio 工具后，创建开发群集。

1. 关闭 PowerShell 窗口。
2. 以管理员身份打开权限提升的新 PowerShell 窗口。 需要执行此步骤来加载最近安装的 Service Fabric 模块。
3. 运行以下 PowerShell 命令创建开发群集：

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster -UseMachineName
    ```

4. 若要启动本地群集管理器工具，请运行以下 PowerShell 命令：

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```

现在，可以开始创建 Service Fabric 网格应用程序！

## <a name="next-steps"></a>后续步骤

仔细阅读[创建 Azure Service Fabric 应用](service-fabric-mesh-tutorial-create-dotnetcore.md)教程。

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.ServiceFabricMesh
[download-visual-studio]: https://www.visualstudio.com/downloads/