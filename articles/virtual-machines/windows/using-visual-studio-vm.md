---
title: 在 Azure 虚拟机上使用 Visual Studio | Microsoft Docs
description: 在 Azure 虚拟机上使用 Visual Studio。
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
ms.date: 03/02/2018
ms.author: phillee
keywords: visualstudio
ms.openlocfilehash: 2c8cd5cb837787db7bd6cb0d76bdf734b7d7bc0f
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285368"
---
# <a name="visual-studio-images-on-azure"></a>Azure 上的 Visual Studio 映像
使用预配置的 Azure 虚拟机 (VM) 中的 Visual Studio 是从无到启动并运行的开发环境的简单快捷方法。 具有不同 Visual Studio 配置的系统映像位于 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps?search=%22visual%20studio%202017%22&page=1)。

不熟悉 Azure？ [创建免费 Azure 帐户](https://azure.microsoft.com/free)。

## <a name="what-configurations-and-versions-are-available"></a>提供了哪些配置和版本？
在 Azure 市场中，可找到最新主版本的映像：Visual Studio 2017 和 Visual Studio 2015。 对于每个主版本，可看到最初发布的 (RTW) 版本和最新更新版本。 其中每个版本都提供 Visual Studio Enterprise 和 Visual Studio Community 版本。 这些映像至少每个月更新一次，以包括最新的 Visual Studio 和 Windows 更新。 尽管映像的名称保持不变，但每个映像的说明包括已安装的产品版本和映像的截止日期。

| 发行版本                                              | 版本                     |     产品版本     |
|:------------------------------------------------------------:|:----------------------------:|:-----------------------:|
| Visual Studio 2017：最新（版本 15.7）                    |    Enterprise、Community     |      版本 15.7.5     |
| Visual Studio 2017：最新预览版（版本 15.8，预览版 5） |    Enterprise、Community     |      版本 15.8.5     |
|         Visual Studio 2017：RTW                              |    Enterprise、Community     |      版本 15.0.15    |
|   Visual Studio 2015：最新 (Update 3)                      |    Enterprise、Community     |  版本 14.0.25431.01  |
|         Visual Studio 2015：RTW                              |             无             | （已过期，无法进行维护） |

> [!NOTE]
> 根据 Microsoft 服务策略，Visual Studio 2015 最初发布的 (RTW) 版本已过期，无法提供服务。 Visual Studio 2015 Update 3 是为 Visual Studio 2015 产品线提供的唯一剩余版本。

有关详细信息，请参阅 [Visual Studio 维护策略](https://www.visualstudio.com/productinfo/vs-servicing-vs)。

## <a name="what-features-are-installed"></a>安装了哪些功能？
每个映像都包含为该 Visual Studio 版本推荐的功能集。 通常，安装包括：

* 所有可用工作负荷，包括每个工作负荷推荐的可选组件
* .NET 4.6.2 和 .NET 4.7 SDK、目标包和开发人员工具
* Visual F#
* 适用于 Visual Studio 的 GitHub 扩展
* LINQ to SQL 工具

生成映像时用于安装 Visual Studio 的命令行如下所示：

```
    vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
       add Microsoft.Net.Component.4.7.SDK ^
       add Microsoft.Net.Component.4.7.TargetingPack ^ 
       add Microsoft.Net.Component.4.6.2.SDK ^
       add Microsoft.Net.Component.4.6.2.TargetingPack ^
       add Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
       add Microsoft.VisualStudio.Component.FSharp ^
       add Component.GitHub.VisualStudio ^
       add Microsoft.VisualStudio.Component.LinqToSql
```

如果映像未包含所需 Visual Studio 功能，请通过页面右上角的反馈工具提供反馈。

## <a name="what-size-vm-should-i-choose"></a>应选择什么大小的 VM？
Azure 提供各种虚拟机大小。 由于 Visual Studio 是一个功能强大的多线程应用程序，因此 VM 大小需要包含至少两个处理器和 7 GB 内存。 我们为 Visual Studio 映像建议以下 VM 大小：

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
有关最新虚拟机大小的详细信息，请参阅 [Azure 中的 Windows 虚拟机大小](/azure/virtual-machines/windows/sizes)。

使用 Azure，可通过调整 VM 大小来重新平衡初始选择。 可为新的 VM 预配更合适的大小，也可调整现有 VM 的大小，使其适应不同的底层硬件。 有关详细信息，请参阅[调整 Windows VM 大小](/azure/virtual-machines/windows/resize-vm)。

## <a name="after-the-vm-is-running-whats-next"></a>VM 运行后，下一步是什么？
Visual Studio 遵循 Azure 中的“自带许可”模式。 与专有硬件上的安装一样，第一步是授权 Visual Studio 安装。 若要解锁 Visual Studio，请执行以下任一操作：
- 使用与 Visual Studio 订阅关联的 Microsoft 帐户登录 
- 使用最初购买附带的产品密钥解锁 Visual Studio

有关详细信息，请参阅[登录 Visual Studio](/visualstudio/ide/signing-in-to-visual-studio) 和[如何解锁 Visual Studio](/visualstudio/ide/how-to-unlock-visual-studio)。

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>如何保存开发 VM 供将来使用或供团队使用？

开发环境的种类繁多，构建更复杂的环境需要付出实际成本。 不管环境如何配置，都可以将已配置的 VM 保存为或捕获为“基础映像”供将来使用或供团队的其他成员使用。 然后，启动新的 VM 时，从基础映像（而不是 Azure 市场映像）对其进行预配。

快速摘要：使用系统准备工具 (Sysprep) 关闭正在运行的 VM，然后通过 Azure 门户的 UI 将 VM 捕获为映像（图 1）。 Azure 会将包含该映像的 `.vhd` 文件保存在所选存储帐户中。 然后，新映像在订阅的资源列表中显示为映像资源。

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*（图 1）通过 Azure 门户 UI 捕获映像。*</center>

有关详细信息，请参阅[在 Azure 中创建通用 VM 的托管映像](/azure/virtual-machines/windows/capture-image-resource)。

> [!IMPORTANT]
> 不要忘记使用 Sysprep 来准备 VM。 如果缺少该步骤，Azure 无法从映像配置 VM。

> [!NOTE]
> 仍需花费一些成本来存储映像，但与从头开始重建 VM 的开销成本相比，对每个需要 VM 的团队成员而言，这种增量成本可能微不足道。 例如，创建和存储 127 GB 的映像每月只需几美元，整个团队都可重复使用该映像。 但是，与每位员工为构建和验证正确配置的开发箱以供个人使用而投入的时间相比，这些成本微不足道。

此外，开发任务或技术可能需要更大的规模，如各种开发配置和多种计算机配置。 可使用 Azure 开发测试实验室，创建可自动构造“黄金映像”的配方。 还可以使用开发测试实验室管理团队正在运行的 VM 策略。 [使用面向开发人员的 Azure 开发测试实验室](/azure/devtest-lab/devtest-lab-developer-lab)是获取有关开发测试实验室更多信息的最佳来源。

## <a name="next-steps"></a>后续步骤
了解预配置的 Visual Studio 映像后，下一步是创建新 VM：

* [通过 Azure 门户创建 VM](quick-create-portal.md)
* [Windows 虚拟机概述](overview.md)
