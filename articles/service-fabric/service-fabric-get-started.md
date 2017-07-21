---
title: "设置 Azure 微服务的开发环境 | Microsoft 文档"
description: "安装运行时、SDK 和工具并创建本地开发群集。 完成此设置后，你就可以开始生成应用程序。"
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
ms.date: 06/20/2017
ms.author: ryanwi, mikhegn
ms.translationtype: Human Translation
ms.sourcegitcommit: f7479260c7c2e10f242b6d8e77170d4abe8634ac
ms.openlocfilehash: 926dfe3de0715f855e6d5b57f10c2366cda8583b
ms.contentlocale: zh-cn
ms.lasthandoff: 06/21/2017


---
# 准备开发环境
<a id="prepare-your-development-environment" class="xliff"></a>
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

 若要在开发计算机上生成并运行 [Azure Service Fabric 应用程序][1]，请安装运行时、SDK 和工具。 此外，还需执行 SDK 中包含的 Windows PowerShell 脚本。

## 先决条件
<a id="prerequisites" class="xliff"></a>
### 支持的操作系统版本
<a id="supported-operating-system-versions" class="xliff"></a>
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

## 安装 SDK 和工具
<a id="install-the-sdk-and-tools" class="xliff"></a>
### 使用 Visual Studio 2017
<a id="to-use-visual-studio-2017" class="xliff"></a>
Service Fabric 工具是 Visual Studio 2017 中 Azure 开发和管理工作负荷的一部分。 在 Visual Studio 安装过程中启用此工作负荷。
此外还需使用 Web 平台安装程序安装 Microsoft Azure Service Fabric SDK。

* [安装 Microsoft Azure Service Fabric SDK][core-sdk]

### 使用 Visual Studio 2015（要求 Visual Studio 2015 Update 2 或更高版本）
<a id="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later" class="xliff"></a>
对于 Visual Studio 2015，Service Fabric 工具是使用 Web 平台安装程序与 SDK 一起安装的：

* [安装 Microsoft Azure Service Fabric SDK 和工具][full-bundle-vs2015]

### 仅安装 SDK
<a id="sdk-installation-only" class="xliff"></a>
如果只需 SDK，可以安装此包：
* [安装 Microsoft Azure Service Fabric SDK][core-sdk]

当前版本包括：
* Service Fabric SDK 2.6.220
* Service Fabric 运行时 5.6.220
* Visual Studio 2015 工具 1.6.50508.2
* Visual Studio 2017 Update 2

当前预览版本包括：
* Service Fabric SDK 255.255.2718.255
* Service Fabric 运行时 255.255.5718.255
* Visual Studio 2015 工具 1.6.50509.5
* Visual Studio 2017 Update 3 Preview 1

有关支持的版本列表，请参阅 [Service Fabric 支持](service-fabric-support.md)

## 启用 PowerShell 脚本执行
<a id="enable-powershell-script-execution" class="xliff"></a>
Service Fabric 使用 Windows PowerShell 脚本创建本地开发群集和部署 Visual Studio 中的应用程序。 默认情况下，Windows 会阻止这些脚本运行。 若要启用它们，你必须修改你的 PowerShell 执行策略。 以管理员身份打开 PowerShell 并输入以下命令：

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## 后续步骤
<a id="next-steps" class="xliff"></a>
完成设置开发环境之后，便可开始生成和运行应用。

* [在 Visual Studio 中创建你的第一个 Service Fabric 应用程序](service-fabric-create-your-first-application-in-visual-studio.md)
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

