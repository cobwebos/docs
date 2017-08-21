---
title: "设置 Azure 微服务的开发环境 | Microsoft 文档"
description: "安装运行时、SDK 和工具并创建本地开发群集。 完成此设置后，便可以开始生成应用程序。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/10/2017
ms.author: ryanwi, mikhegn
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: f0c6957217c21bdfd76498944e248fc808f2d271
ms.contentlocale: zh-cn
ms.lasthandoff: 08/11/2017

---
# <a name="prepare-your-development-environment"></a>准备开发环境
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

 若要在开发计算机上生成并运行 [Azure Service Fabric 应用程序][1]，请安装运行时、SDK 和工具。 此外，还需执行 SDK 中包含的 Windows PowerShell 脚本。

## <a name="prerequisites"></a>先决条件
### <a name="supported-operating-system-versions"></a>支持的操作系统版本
支持使用以下操作系统版本进行开发：

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> 默认情况下，Windows 7 仅包含 Windows PowerShell 2.0。 Service Fabric PowerShell cmdlet 需要 PowerShell 3.0 或更高版本。 可以从 Microsoft 下载中心[下载 Windows PowerShell 5.0][powershell5-download]。
> 
> 

## <a name="install-the-sdk-and-tools"></a>安装 SDK 和工具
### <a name="to-use-visual-studio-2017"></a>使用 Visual Studio 2017
Service Fabric 工具是 Visual Studio 2017 中 Azure 开发和管理工作负荷的一部分。 在 Visual Studio 安装过程中启用此工作负荷。
此外还需使用 Web 平台安装程序安装 Microsoft Azure Service Fabric SDK。

* [安装 Microsoft Azure Service Fabric SDK][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>使用 Visual Studio 2015（要求 Visual Studio 2015 Update 2 或更高版本）
对于 Visual Studio 2015，Service Fabric 工具是使用 Web 平台安装程序与 SDK 一起安装的：

* [安装 Microsoft Azure Service Fabric SDK 和工具][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>仅安装 SDK
如果只需 SDK，可以安装此包：
* [安装 Microsoft Azure Service Fabric SDK][core-sdk]

当前版本包括：
* Service Fabric SDK 2.7.198
* Service Fabric 运行时 5.7.198
* 用于 Visual Studio 2015 1.7.50721 的 Service Fabric 工具
* Visual Studio 2017 Update 2 包括用于 Visual Studio 1.6.20170504 的 Service Fabric 工具
* Visual Studio 2017 Update 3 Preview 7 (15.3.0 Preview 7.0) 包括用于 Visual Studio 1.7.20170721 的 Service Fabric 工具

有关支持的版本列表，请参阅 [Service Fabric 支持](service-fabric-support.md)

## <a name="enable-powershell-script-execution"></a>启用 PowerShell 脚本执行
Service Fabric 使用 Windows PowerShell 脚本创建本地开发群集和部署 Visual Studio 中的应用程序。 默认情况下，Windows 会阻止这些脚本运行。 要启用它们，必须修改 PowerShell 执行策略。 以管理员身份打开 PowerShell 并输入以下命令：

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="next-steps"></a>后续步骤
完成设置开发环境之后，便可开始生成和运行应用。

* 
            [在 Visual Studio 中创建第一个 Service Fabric 应用程序](service-fabric-create-your-first-application-in-visual-studio.md)
* [了解如何在本地群集上部署和管理应用程序](service-fabric-get-started-with-a-local-cluster.md)
* [了解编程模型：Reliable Services 和 Reliable Actors](service-fabric-choose-framework.md)
* [在 GitHub 上检查 Service Fabric 代码示例](https://aka.ms/servicefabricsamples)
* [使用 Service Fabric 资源管理器可视化群集](service-fabric-visualizing-your-cluster.md)
* [按照 Service Fabric 学习路径获取平台的概括性介绍](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
* 了解 [Service Fabric 支持选项](service-fabric-support.md)

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "Service Fabric 活动页"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI 链接"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI 链接"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI 链接"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395

