---
title: "当你使用服务生成的应用程序主要差异 Azure 和 Azure 堆栈 |Microsoft 文档"
description: "您需要知道当你使用服务或生成适用于 Azure 堆栈应用。"
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 1e170f320292e3dbe920907a4ed81ab0d1eb388b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="key-considerations-using-services-or-building-apps-for-azure-stack"></a>关键注意事项： 使用服务或 Azure 堆栈为构建应用

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

当你使用服务，或生成适用于 Azure 堆栈应用时，你必须了解有 Azure 堆栈与 Azure 之间的差异。 本文的重要注意事项的概述，当你面向 Azure 堆栈作为混合云开发环境。

## <a name="overview"></a>概述

Azure Stack 是一个混合云平台，可用于通过公司或服务提供商的数据中心使用 Azure 服务。 作为开发人员，你可以生成 Azure 堆栈运行的应用。 然后可以将这些应用程序部署到 Azure 堆栈，到 Azure，或者你可以真正构建利用 Azure 堆栈云与 Azure 之间的连接的混合应用程序。

Azure 堆栈运算符将让你知道哪些服务是可供你使用，以及如何获取支持。 它们提供通过其自定义的计划和提供这些服务。

Azure 技术内容假定正在开发的一项 Azure 服务而不是 Azure 堆栈应用。 当生成和将应用部署到 Azure 堆栈时，你必须了解的主要区别，如所示：

* Azure 堆栈提供的服务和在 Azure 中提供的功能的子集。
* 你的公司或服务提供商可以选择他们想要提供哪些的服务。 这包括自定义的服务或应用程序。 它们可能会提供其自己的自定义的文档。
* 你必须使用正确 （例如，门户的地址和 Azure 资源管理器终结点的 Url） 的堆栈的特定于 Azure 终结点。
* 你必须使用支持的 Azure 堆栈的 PowerShell 和 API 版本。 这样做可确保你的应用程序，将在 Azure 堆栈和 Azure 中正常工作。

## <a name="cheat-sheet-high-level-differences"></a>速查表： 高层差异

下表描述 Azure 堆栈和 Azure 之间的高级差异。 当你开发用于 Azure 堆栈或使用 Azure 堆栈服务，请记住这几点。

| 区域 | Azure （全局） | Azure Stack |
| -------- | ------------- | ----------|
| 用户运行它？ | Microsoft | 你的组织或服务提供程序。|
| 您与人员寻求支持？ | Microsoft | 对于集成系统，请与支持你 Azure 堆栈运算符 （在你的组织或服务提供商）。<br><br>获取 Azure 堆栈开发工具包支持，请访问[Microsoft 论坛](https://social.msdn.microsoft.com/Forums/home?forum=azurestack)。 由于开发工具包是评估环境，没有提供通过 Microsoft 客户支持服务 (CSS) 正式支持。
| 可用服务 | 请参阅列表[Azure 产品](https://azure.microsoft.com/services/?b=17.04b)。 可用服务因 Azure 区域而异。 | Azure 堆栈支持 Azure 服务的子集。 实际服务取决于你的组织或服务提供商选择提供。
| Azure 资源管理器终结点 * | https://management.azure.com | 对于 Azure 堆栈集成系统，使用 Azure 堆栈运算符提供的终结点。<br><br>对于开发工具包中，使用： https://management.local.azurestack.external
| 门户 URL * | [https://portal.azure.com](https://portal.azure.com) | 对于 Azure 堆栈集成系统，转至 Azure 堆栈运算符提供的 URL。<br><br>对于开发工具包中，使用： https://portal.local.azurestack.external
| 区域 | 你可以选择想要部署到哪些的区域。 | 对于 Azure 堆栈集成系统，使用你的系统可用的区域。<br><br>对于开发工具包中，将始终为区域**本地**。
| 资源组 | 资源组可以跨区域。 | 有关集成的系统和开发工具包中，没有只有一个区域。
|支持的命名空间、 资源类型和 API 版本 | 最新 （或不推荐使用的早期版本）。 | Azure 堆栈支持特定版本。 请参阅此文章的"版本要求"部分。
| | |

* 如果你是 Azure 堆栈操作员，请参阅[使用管理员门户](../azure-stack-manage-portals.md)和[管理基础知识](../azure-stack-manage-basics.md)有关详细信息。

## <a name="helpful-tools-and-best-practices"></a>有用的工具和最佳做法
 
 Microsoft 提供的若干工具和指南可帮助你为 Azure 堆栈的开发。

| 建议 | 参考 | 
| -------- | ------------- | 
| 在开发工作站上安装正确的工具。 | - [安装 PowerShell](azure-stack-powershell-install.md)<br>- [下载工具](azure-stack-powershell-download.md)<br>- [配置 PowerShell](azure-stack-powershell-configure-user.md)<br>- [安装 Visual Studio](azure-stack-install-visual-studio.md) 
| 查看以下信息：<br>-Azure 资源管理器模板注意事项<br>-如何查找快速入门模板<br>-使用策略模块来帮助你使用 Azure 可开发用于 Azure 堆栈 | [Azure Stack 开发](azure-stack-developer.md) | 
| 查看并按照模板的最佳做法。 | [资源管理器快速入门模板](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#best-practices)
| | |

## <a name="version-requirements"></a>版本要求

Azure 堆栈支持特定版本的 Azure PowerShell 和 Azure 服务 Api。 必须使用支持的版本以确保你的应用程序可以将部署到这两个 Azure 堆栈和 Azure。

若要确保使用正确版本的 Azure PowerShell，使用[API 版本配置文件](azure-stack-version-profiles.md)。 若要确定你可以使用的最新 API 版本配置文件，你必须知道哪个 Azure 堆栈你正在使用的生成。 你可以从 Azure 堆栈管理员获取此信息。

>[!NOTE]
 如果你使用的 Azure 堆栈开发工具包中，并且具有管理访问权限，请参阅的"确定的当前版本"部分[管理更新](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#determine-the-current-version)以确定 Azure 堆栈生成。

对于其他 Api，运行以下 PowerShell 命令输出的命名空间、 资源类型和 Azure 堆栈订阅中支持的 API 版本。 注意有可能仍有在属性级别的差异。 (对于此命令中工作，你必须具有已[安装](azure-stack-powershell-install.md)和[配置](azure-stack-powershell-configure-user.md)PowerShell for Azure 堆栈环境。 你必须还具有 Azure 堆栈服务关联的订阅。）

 ```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

（已截断） 的示例输出： ![Get-azurermresourceprovider 命令示例输出](media/azure-stack-considerations/image1.png)
 
## <a name="next-steps"></a>后续步骤

有关在服务级别的区别的更多详细信息，请参阅：

* [Azure 堆栈中的虚拟机的注意事项](azure-stack-vm-considerations.md)
* [有关 Azure 堆栈中的存储注意事项](azure-stack-acs-differences.md)
* [有关 Azure 堆栈网络注意事项](azure-stack-network-differences.md)
