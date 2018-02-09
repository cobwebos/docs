---
title: "在 Azure 虚拟机上使用 Visual Studio | Microsoft Docs"
description: "在 Azure 虚拟机上使用 Visual Studio。"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: PhilLee-MSFT
manager: sacalla
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.prod: vs-devops-alm
ms.date: 01/30/2018
ms.author: phillee
keywords: visualstudio
ms.openlocfilehash: 813022f1778e2c7f3174e11192b845c2c33ad219
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a id="top"> </a> Azure 上的 Visual Studio 映像
使用预配置的 Azure 虚拟机 (VM) 中运行的 Visual Studio 是从无到启动并运行的开发环境的最简单最快捷的方法。  具有不同 Visual Studio 配置的系统映像位于 [Azure Marketplace](https://portal.azure.com/)。 只需启动 VM 即可离开。

不熟悉 Azure？ [创建免费 Azure 帐户](https://azure.microsoft.com/free)。

## <a name="what-configurations-and-versions-are-available"></a>提供了哪些配置和版本？
在 Azure Marketplace 中，可找到最新主版本的映像：Visual Studio 2017 和 Visual Studio 2015。  对于每个主版本，可看到最初发布的（又名“RTW”）版本和“最新”更新版本。  对于这些不同版本，都可找到 Visual Studio Enterprise 和 Visual Studio Community 版本。

|               发行版本              |          版本            |    产品版本    |
|:------------------------------------------:|:----------------------------:|:---------------------:|
| Visual Studio 2017 - 最新版本（版本 15.5） |    Enterprise、Community     |     版本 15.5.3    |
|         Visual Studio 2017 - RTW           |    Enterprise、Community     |     版本 15.0.7    |
|   Visual Studio 2015 - 最新版本 (Update 3)   |    Enterprise、Community     | 版本 14.0.25431.01 |
|         Visual Studio 2015 - RTW           | 无（已过期，无法进行维护） |          ---          |

> [!NOTE]
> 根据 Microsoft 维护策略，Visual Studio 2015 最初发布的版本（又名“RTW”）已过期，无法进行维护。  因此，Visual Studio 2015 Update 3 是为 Visual Studio 2015 产品线提供的唯一剩余版本。

有关详细信息，请参阅 [Visual Studio 维护策略](https://www.visualstudio.com/en-us/productinfo/vs-servicing-vs)。

## <a name="what-features-are-installed"></a>安装了哪些功能？
每个映像都包含为该 Visual Studio 版本推荐的功能集。  通常，安装包括：

* 所有可用工作负载，包括该工作负载推荐的可选组件
* .NET 4.6.2 和 .NET 4.7 SDK、目标包和开发人员工具
* Visual F#
* 适用于 Visual Studio 的 GitHub 扩展
* LINQ to SQL 工具

这是我们用来在生成映像时安装 Visual Studio 的命令行：

   * vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
   * add Microsoft.Net.Component.4.7.SDK ^
   * add Microsoft.Net.Component.4.7.TargetingPack ^ 
   * add Microsoft.Net.Component.4.6.2.SDK ^
   * add Microsoft.Net.Component.4.6.2.TargetingPack ^
   * add Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
   * add Microsoft.VisualStudio.Component.FSharp ^
   * add Component.GitHub.VisualStudio ^
   * add Microsoft.VisualStudio.Component.LinqToSql

如果映像未包含所需 Visual Studio 功能，请通过反馈工具（页面右上角）提供反馈。

## <a name="what-size-vm-should-i-choose"></a>应选择什么大小的 VM？
预配新的虚拟机非常简单，Azure 提供了各种虚拟机大小。  与任何硬件采购一样，需要平衡性能与成本间的关系。  由于 Visual Studio 是一个功能强大的多线程应用程序，因此 VM 大小需要包含至少两个处理器和 7 GB 内存。  有关最新虚拟机大小的详细信息，请参阅 [Azure 中的 Windows 虚拟机大小](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes)。

使用 Azure，你不会陷入首个选择 - 可通过调整 VM 大小来重新平衡初始选择。  可使用更合适的大小预配新的 VM，也可调整现有 VM 的大小，使其适应不同的基础硬件。  有关详细信息，请参阅[调整 Windows VM 大小](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/resize-vm)。

## <a name="after-i-get-the-vm-running-then-what"></a>运行 VM 后，会发生什么情况？
Visual Studio 遵循 Azure 中的“自带许可”模式。  因此，与专有硬件上的安装类似，第一步是授权 Visual Studio 安装。  可通过使用与 Visual Studio 订阅关联的 Microsoft 帐户进行登录来解锁 Visual Studio，也可在初始购买时使用产品密钥解锁 Visual Studio。  有关详细信息，请参阅[登录 Visual Studio](https://docs.microsoft.com/en-us/visualstudio/ide/signing-in-to-visual-studio) 和[如何解锁 Visual Studio](https://docs.microsoft.com/en-us/visualstudio/ide/how-to-unlock-visual-studio)。

## <a name="after-i-build-out-the-dev-vm-how-do-i-save-capture-it-for-future-or-team-use"></a>构建开发 VM 后，如何保存（捕获）它以供将来或团队使用？

开发环境的种类繁多，构建更复杂的环境需要付出实际成本。  但是，无论环境配置如何，Azure 都可通过将完美配置的 VM 保存/捕获为“基本映像”以备将来使用（供你和/或团队的其他成员使用），从而轻松地保留该投资。  然后，启动新的 VM 时，从基本映像（而不是从 Marketplace 映像）中对其进行配置。

简要总结，需要 Sysprep 并关闭正在运行的 VM，然后通过 Azure 门户的用户界面将 VM 捕获为映像（图 1）。  Azure 会将包含该映像的 `.vhd` 文件保存在所选存储帐户中。  然后，新映像在订阅的资源列表中显示为映像资源。

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal’s UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*（图 1）通过 Azure 门户的用户界面捕获映像。*</center>

有关详细信息，请参阅[将 VM 捕获为映像](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/capture-image-resource)。

  **提醒：**不要忘记 Sysprep VM ！  如果缺少该步骤，Azure 无法从映像配置 VM。

> [!NOTE]
> 仍需花费一些成本来存储映像，但与从头开始重建 VM 的人力成本相比，对团队中需要 VM 的每个人而言，这种增量成本可能微不足道。  例如，创建和存储 127 GB 的映像每月只需几美元，团队所有成员都可重复使用该映像。  但是，与每位员工为构建和验证正确配置的开发箱以供个人使用而投入的时间相比，这些成本微不足道。

此外，开发任务或技术可能需要更大的规模 - 如各种开发配置和多种计算机配置。  可使用 Azure 开发测试实验室，创建可自动构造“黄金映像”的配方，并管理团队正在运行的 VM 策略。  [使用面向开发人员的 Azure 开发测试实验室](https://docs.microsoft.com/en-us/azure/devtest-lab/devtest-lab-developer-lab)是获取有关开发测试实验室更多信息的最佳来源。

## <a name="next-steps"></a>后续步骤
了解预配置的 Visual Studio 映像后，下一步是创建新 VM：

* [通过 Azure 门户创建 VM](quick-create-portal.md)
* [Windows 虚拟机概述](overview.md)
