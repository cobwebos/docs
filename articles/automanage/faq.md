---
title: 适用于虚拟机的 Azure Automanage 常见问题解答
description: 有关 Azure Automanage for virtual 计算机的常见问题的解答。
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: troubleshooting
ms.date: 09/04/2020
ms.author: deanwe
ms.openlocfilehash: 348106c405e6e096f7bfd9a225fc783c4454b1ad
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449770"
---
# <a name="frequently-asked-questions-for-azure-automanage-for-vms"></a>Azure Automanage for Vm 常见问题

本文提供了有关 [Azure Automanage for 虚拟机](automanage-virtual-machines.md)的一些最常见问题的解答。

如果本文未解决 Azure 问题，请访问 [MSDN 和 Stack Overflow](https://azure.microsoft.com/support/forums/) 上的 Azure 论坛。 可将问题发布到这些论坛上，或发布到 [Twitter 上的 @AzureSupport](https://twitter.com/AzureSupport)。 还可提交 Azure 支持请求。 若要提交支持请求，请在 [Azure 支持页](https://azure.microsoft.com/support/options/)上，选择 " **获取支持**"。


## <a name="azure-automanage-for-virtual-machines"></a>适用于虚拟机的 Azure Automanage

**启用 Azure Automanage 需要满足哪些先决条件？**

若要启用 Azure Automanage，需要满足以下先决条件：
- 仅限 Windows Server Vm
- Vm 必须正在运行
- Vm 必须位于受支持的区域
- 用户必须具有正确的权限
- 仅限非规模集 Vm
- Vm 不得链接到不同订阅中的 log analytics 工作区
- Automanage 目前不支持沙盒订阅

**启用 Automanage 需要哪些 RBAC 权限？**

如果要在具有现有 Automanage 帐户的 VM 上启用 Automanage，则需要具有 VM 所在的资源组的参与者角色。

如果在启用时使用的是新的 Automanage 帐户，则必须具有 "所有者" 角色或 "参与者" 和 "用户访问管理员" 角色。


**哪些区域受支持？**

支持以下区域中的 Vm：西欧、美国东部、美国西部2、加拿大中部、美国西部。


**Azure Automanage 自动执行哪些功能？**

Automanage 在 VM 的整个生命周期中注册、配置和监视 [此处](virtual-machines-best-practices.md)列出的服务。

**Azure Automanage 是否可用于启用了 Azure Arc 的 Vm？**

Automanage 当前不支持启用了 Arc 的 Vm。

**能否在 Azure Automanage 上自定义配置？**

客户可通过配置首选项自定义特定服务（如 Azure 备份保留）的设置。 有关可更改的设置的完整列表，请参阅 [此处](virtual-machines-best-practices.md)的文档。


**Azure Automanage 是否适用于 Linux 和 Windows Vm？**

目前，Automanage 支持 Windows Server Azure Vm。


**是否可以有选择性地在一组 Vm 上应用 Automanage？**

在选定的新的和现有的 Vm 上，可以通过单击和点简单的方式来启用 Automanage。 Automanage 也可以随时禁用。


**Azure Automanage 是否支持虚拟机规模集中的 Vm？**

否，Azure Automanage 当前不支持虚拟机规模集中的 Vm。


**Azure Automanage 的费用是多少？**

在公共预览版中，无需额外付费即可使用 Azure Automanage。 附加的 Azure 资源（例如 Azure 备份）会产生费用。


**能否通过 Azure 策略应用 Automanage？**

是的，我们有一个用于自动将 Automanage 应用于定义范围内的所有 Vm 的内置策略。 你还将指定配置文件 (开发测试或生产) 连同你的 Automanage 帐户一起指定。 若要详细了解如何通过 Azure 策略启用 Automanage，请参阅 [此处](virtual-machines-policy-enable.md)。


**什么是 Automanage 帐户？**

Automanage 帐户是一个 MSI (托管服务标识) ，它提供了用于执行自动操作的安全上下文或标识。


**启用 Automanage 时，它是否会影响除 VM 之外的任何其他 Vm) 是否选择了 VM (？**

如果 VM 已链接到现有 Log Analytics 工作区，我们将重复使用该工作区来应用这些解决方案：更改跟踪、清点和更新管理。 连接到该工作区的所有 Vm 都将启用这些解决方案。


**能否更改 VM 的配置文件？**

此时，你将需要为该 VM 禁用 Automanage，然后使用所需的配置文件和首选项重新启用 Automanage。


**如果已为某个服务（例如更新管理）配置了我的 VM，将 Automanage 重新配置它？**
不可以，Automanage 不会重新配置它。 我们会开始监视与该服务关联的资源，以获得偏差。


**为什么 VM 在 Automanage 门户中的状态为 "失败"？**

如果状态显示为 " *失败*"，则可以通过 VM 所在的资源组对部署进行故障排除。 中转到 " **资源组**"，选择资源组，单击 " **部署** "， *并在其中* 显示错误详细信息。

**如何获取 Automanage 的故障排除支持？**

您可以使用 [技术支持案例](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。 对于 "**服务**" 选项，搜索并选择 "*监视和管理*" 部分下的*Automanage* 。


## <a name="next-steps"></a>后续步骤

尝试在 Azure 门户中为虚拟机启用 Automanage。

> [!div class="nextstepaction"]
> [在 Azure 门户中为虚拟机启用 Automanage](quick-create-virtual-machines-portal.md)