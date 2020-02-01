---
title: 设置 Windows 开发环境
description: 安装运行时、SDK 和工具并创建本地开发群集。 完成此设置后，你将可以在 Windows 上生成应用程序。
author: peterpogorski
ms.topic: conceptual
ms.date: 01/28/2020
ms.custom: sfrev
ms.openlocfilehash: 70a819636b786cedce5bbd0abd0d2594738cbfb9
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76904106"
---
# <a name="prepare-your-development-environment-on-windows"></a>在 Windows 上准备开发环境

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>

若要在 Windows 开发计算机上生成并运行[Azure Service Fabric 应用程序][1]，请安装 Service Fabric 运行时、SDK 和工具。 还需要启用 SDK 中包含的[Windows PowerShell 脚本的执行](#enable-powershell-script-execution)。

## <a name="prerequisites"></a>Prerequisites

### <a name="supported-operating-system-versions"></a>支持的操作系统版本

支持以下操作系统版本进行开发：

* Silverlight
* Windows 8/Windows 8。1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Windows 7 支持：
> - 默认情况下，windows 7 仅包含 Windows PowerShell 2.0。 Service Fabric PowerShell cmdlet 需要 PowerShell 3.0 或更高版本。 你可以从 Microsoft 下载中心[下载 Windows PowerShell 5.1][powershell5-download] 。
> - Service Fabric 反向代理在 Windows 7 上不可用。

## <a name="install-the-sdk-and-tools"></a>安装 SDK 和工具

建议使用 Web 平台安装程序（WebPI）来安装 SDK 和工具。 如果使用 WebPI 收到运行时错误，还可以在特定 Service Fabric 版本的发行说明中找到指向安装程序的直接链接。 可以在[Service Fabric 团队博客](https://blogs.msdn.microsoft.com/azureservicefabric/)上的各种发布公告中找到发行说明。

> [!NOTE]
> 不支持本地 Service Fabric 开发群集升级。

### <a name="to-use-visual-studio-2017-or-2019"></a>使用 Visual Studio 2017 或2019

Service Fabric 工具是 Visual Studio 2017 和2019中的 Azure 开发工作负荷的一部分。 在 Visual Studio 安装过程中启用此工作负荷。
此外，需要使用 Web 平台安装程序安装 Microsoft Azure Service Fabric SDK 和运行时。

* [安装 Microsoft Azure Service Fabric SDK][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>使用 Visual Studio 2015 （需要 Visual Studio 2015 Update 2 或更高版本）

对于 Visual Studio 2015，使用 Web 平台安装程序与 SDK 和运行时一起安装 Service Fabric 工具：

* [安装 Microsoft Azure Service Fabric SDK 和工具][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>仅限 SDK 安装

如果只需要 SDK，可以安装此包：

* [安装 Microsoft Azure Service Fabric SDK][core-sdk]

当前版本包括：

* Service Fabric SDK 和工具4.0.464
* Service Fabric 运行时7.0.464

有关支持的版本的列表，请参阅[Service Fabric 版本](service-fabric-versions.md)

> [!NOTE]
> 应用程序或群集升级不支持单计算机群集（OneBox）;如果需要执行群集升级，或者在执行应用程序升级时遇到任何问题，请删除 OneBox 群集并重新创建它。 

## <a name="enable-powershell-script-execution"></a>启用 PowerShell 脚本执行

Service Fabric 使用 Windows PowerShell 脚本创建本地开发群集和部署 Visual Studio 中的应用程序。 默认情况下，Windows 会阻止这些脚本运行。 若要启用它们，必须修改 PowerShell 执行策略。 以管理员身份打开 PowerShell 并输入以下命令：

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="install-docker-optional"></a>安装 Docker （可选）

[Service Fabric 是](service-fabric-containers-overview.md)用于跨计算机群集部署微服务的容器 orchestrator。 若要在本地开发群集上运行 Windows 容器应用程序，必须先安装用于 Windows 的 Docker。 获取[用于 Windows 的 Docker CE （稳定）](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)。 安装并启动 Docker 后，右键单击任务栏图标，然后选择 "**切换到 Windows 容器**"。 此步骤是运行基于 Windows 的 Docker 映像所必需的。

## <a name="next-steps"></a>后续步骤

完成开发环境设置后，开始生成和运行应用。

* [了解如何创建、部署和管理应用程序](service-fabric-tutorial-create-dotnet-app.md)
* [了解编程模型： Reliable Services 和 Reliable Actors](service-fabric-choose-framework.md)
* [查看 GitHub 上的 Service Fabric 代码示例](https://aka.ms/servicefabricsamples)
* [使用 Service Fabric Explorer 可视化群集](service-fabric-visualizing-your-cluster.md)
* 了解[Service Fabric 支持选项](service-fabric-support.md)

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "Service Fabric 活动页"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI 链接"
[full-bundle-dev15]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI 链接"
[core-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI 链接"
[powershell5-download]:https://www.microsoft.com/download/details.aspx?id=54616
