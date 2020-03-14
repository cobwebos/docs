---
title: 为 Service Fabric 网格设置 Windows 开发环境
description: 设置 Windows 开发环境，以便可以创建 Service Fabric 网格应用程序并将其部署到 Azure Service Fabric 网格。
author: dkkapur
ms.author: dekapur
ms.date: 12/12/2018
ms.topic: conceptual
ms.openlocfilehash: a674047722d4deca02d8f4d38a0826e479065037
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259196"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>设置 Windows 开发环境以生成 Service Fabric 网格应用

若要在 Windows 开发计算机上生成并运行 Azure Service Fabric 网格应用程序，需要具有以下项：

* Docker
* Visual Studio 2017 或更高版本
* Service Fabric 网格运行时
* Service Fabric 网格 SDK 和工具。

以及下列 Windows 版本之一：

* Windows 10（企业版、专业版或教育版）版本 1709 (Fall Creators Update) 或 1803 (Windows 10 April 2018 Update)
* Windows Server 版本 1709
* Windows Server 版本 1803

以下说明将帮助你根据所运行的 Windows 版本来获取所安装的所有内容。

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="visual-studio"></a>Visual Studio

部署 Service Fabric 网格应用程序需要 Visual Studio 2017 或更高版本。 [安装版本 15.6.0][download-visual-studio]或更高版本并启用以下工作负载：

* ASP.NET 和 Web 开发
* Azure 开发

## <a name="install-docker"></a>安装 Docker

如果已安装 Docker，请确保具有最新版本。 当新版本推出时，Docker 可能会提示你，但请手动检查以确保具有最新版本。

#### <a name="install-docker-on-windows-10"></a>在 Windows 10 上安装 Docker

下载并安装最新版本的[适用于 Windows 的 Docker 社区版][download-docker]，以支持 Service Fabric Service Fabric 网格使用的容器化应用程序。

在安装过程中出现提示时，请选择“使用 Windows 容器而不是 Linux 容器”。

如果在你的计算机上未启用 Hyper-v，则 Docker 的安装程序将启用它。 如果出现提示，单击“确定”以执行此操作。

#### <a name="install-docker-on-windows-server-2016"></a>在 Windows Server 2016 上安装 Docker

如果未启用 Hyper-V 角色，请以管理员身份打开 PowerShell 并运行以下命令以启用 Hyper-V，然后重启计算机。 有关详细信息，请参阅[Docker Enterprise Edition For Windows Server][download-docker-server]。

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

重启计算机。

以管理员身份打开 PowerShell 并运行以下命令来安装 Docker：

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sdk-and-tools"></a>SDK 和工具

按以下顺序安装 Service Fabric 网格运行时、SDK 和工具。

1. 使用 Web 平台安装程序安装[Service Fabric 网格 SDK][download-sdkmesh] 。 这也会安装 Microsoft Azure Service Fabric SDK 和运行时。
2. 从 Visual Studio Marketplace 安装[Visual Studio Service Fabric 网格工具（预览版）扩展][download-tools]。

## <a name="build-a-cluster"></a>生成群集

> [!IMPORTANT]
> 在生成群集之前，Docker **必须**正在运行。
> 若要测试 Docker 是否正在运行，请打开一个终端窗口，运行 `docker ps` 并查看是否出错。 如果响应中未指示错误，则表示 Docker 正在运行，可以生成群集。

> [!Note]
> 如果是在 Windows Fall Creators Update（版本 1709）计算机上进行开发，则只能使用 Windows 1709 版本的 Docker 映像。
> 如果是在 Windows 10 April 2018 Update（版本 1803）计算机上进行开发，则可以使用 Windows 1709 或 1803 版本的 Docker 映像。

如果你使用的是 Visual Studio，则可以跳过此部分，因为如果没有本地群集，Visual Studio 会为你创建一个。

若要在每次创建和运行单个 Service Fabric 应用时获得最佳的调试性能，请创建单节点本地开发群集。 如果一次运行多个应用程序，则创建一个五节点本地开发群集。 每当部署或调试 Service Fabric 网格项目时，都必须运行此群集。

安装运行时、SDK、Visual Studio 工具、Docker 并运行 Docker 之后，创建一个开发群集。

1. 关闭 PowerShell 窗口。
2. 以管理员身份打开权限提升的新 PowerShell 窗口。 需要执行此步骤来加载最近安装的 Service Fabric 模块。
3. 运行以下 PowerShell 命令创建开发群集：

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateMeshCluster -CreateOneNodeCluster
    ```
4. 若要启动本地群集管理器工具，请运行以下 PowerShell 命令：

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```
5. 服务群集管理器工具运行后（它出现在系统托盘中），右键单击它并单击“启动本地群集”。

![图 1 - 启动本地群集](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

现在，可以开始创建 Service Fabric 网格应用程序！

## <a name="next-steps"></a>后续步骤

仔细阅读[创建 Azure Service Fabric 应用](service-fabric-mesh-tutorial-create-dotnetcore.md)教程。

查找[常见问题和已知问题](service-fabric-mesh-faq.md)的答案。

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/