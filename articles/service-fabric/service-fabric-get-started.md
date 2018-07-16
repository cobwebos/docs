---
title: 设置 Azure 微服务的 Windows 开发环境 | Microsoft Docs
description: 安装运行时、SDK 和工具并创建本地开发群集。 完成此设置后，就可以在 Windows 上开始生成应用程序。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: ryanwi
ms.openlocfilehash: 993d5c804490540a9c71647f52692fb55b27c405
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113293"
---
# <a name="prepare-your-development-environment-on-windows"></a>在 Windows 上准备开发环境
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

若要在 Windows 开发计算机上生成并运行 [Azure Service Fabric 应用程序][1]，请安装 Service Fabric 运行时、SDK 和工具。 此外，还需要启用 SDK 中包含的 [Windows PowerShell 脚本执行](#enable-powershell-script-execution)。

## <a name="prerequisites"></a>先决条件
### <a name="supported-operating-system-versions"></a>支持的操作系统版本
支持使用以下操作系统版本进行开发：

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Windows 7 支持：
> - 默认情况下，Windows 7 仅包含 Windows PowerShell 2.0。 Service Fabric PowerShell cmdlet 需要 PowerShell 3.0 或更高版本。 可以从 Microsoft 下载中心[下载 Windows PowerShell 5.0][powershell5-download]。
> - 在 Windows 7 上无法使用 Service Fabric 反向代理。
>

## <a name="install-the-sdk-and-tools"></a>安装 SDK 和工具
### <a name="to-use-visual-studio-2017"></a>使用 Visual Studio 2017
Service Fabric 工具是 Visual Studio 2017 中 Azure 开发工作负荷的一部分。 在 Visual Studio 安装过程中启用此工作负荷。
此外还需使用 Web 平台安装程序安装 Microsoft Azure Service Fabric SDK 和运行时。

* [安装 Microsoft Azure Service Fabric SDK][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>使用 Visual Studio 2015（要求 Visual Studio 2015 Update 2 或更高版本）
对于 Visual Studio 2015，Service Fabric 工具是使用 Web 平台安装程序与 SDK 和运行时 一起安装的：

* [安装 Microsoft Azure Service Fabric SDK 和工具][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>仅安装 SDK
如果只需 SDK，可以安装此包：
* [安装 Microsoft Azure Service Fabric SDK][core-sdk]

当前版本包括：
* Service Fabric SDK and Tools 3.1.301
* Service Fabric 运行时 6.2.301
* Service Fabric Tools for Visual Studio 2015 2.1.20180510.2
* Visual Studio 2017 15.7 包括 Service Fabric Tools for Visual Studio 2.1.20180423.1 

有关支持的版本列表，请参阅 [Service Fabric 支持](service-fabric-support.md)

## <a name="enable-powershell-script-execution"></a>启用 PowerShell 脚本执行
Service Fabric 使用 Windows PowerShell 脚本创建本地开发群集和部署 Visual Studio 中的应用程序。 默认情况下，Windows 会阻止这些脚本运行。 要启用它们，必须修改 PowerShell 执行策略。 以管理员身份打开 PowerShell 并输入以下命令：

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="next-steps"></a>后续步骤
完成设置开发环境之后，便可开始生成和运行应用。

* [在 Visual Studio 中创建第一个 Service Fabric 应用程序](service-fabric-create-your-first-application-in-visual-studio.md)
* [了解如何在本地群集上部署和管理应用程序](service-fabric-get-started-with-a-local-cluster.md)
* [在 Windows 上准备 Linux 开发环境](service-fabric-local-linux-cluster-windows.md)
* [了解编程模型：Reliable Services 和 Reliable Actors](service-fabric-choose-framework.md)
* [在 Azure Service Fabric 中定期备份和还原](service-fabric-backuprestoreservice-quickstart-azurecluster.md)
* [在 GitHub 上检查 Service Fabric 代码示例](https://aka.ms/servicefabricsamples)
* [使用 Service Fabric 资源管理器可视化群集](service-fabric-visualizing-your-cluster.md)
* [按照 Service Fabric 学习路径获取平台的概括性介绍](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
* 了解 [Service Fabric 支持选项](service-fabric-support.md)
* [在群集上自动进行操作系统修补](service-fabric-patch-orchestration-application.md)

[1]: http://azure.microsoft.com/campaigns/service-fabric/ "Service Fabric 活动页"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI 链接"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI 链接"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI 链接"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395
