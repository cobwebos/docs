---
title: 使用 Azure Stack 门户 | Microsoft Docs
description: 了解如何访问和使用 Azure Stack 中的用户门户。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: mabrigg
ms.reviewer: efemmano
ms.openlocfilehash: 7ca29ee359349f69c3d5ff21bd9db3f93358206a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724306"
---
# <a name="use-the-azure-stack-portal"></a>使用 Azure Stack 门户

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用 Azure Stack 门户订阅公共套餐，并使用这些套餐提供的服务。 如果你以前用过全球 Azure 门户，则应当已经熟悉了该站点的工作原理。

## <a name="access-the-portal"></a>访问门户

Azure Stack 操作员（服务提供商或组织中的管理员）将会告知门户的正确 URL。

- 对于集成系统，URL 根据操作员所在的区域和外部域名的不同而异，格式为 https://portal.&lt;*region*&gt;.&lt;*FQDN*&gt;。
- 如果使用的是 Azure Stack 开发工具包，则门户地址为 https://portal.local.azurestack.external。

![Azure 堆栈用户门户的屏幕捕获](media/azure-stack-use-portal/UserPortal.png)

## <a name="customize-the-dashboard"></a>自定义仪表板

仪表板包含一组默认磁贴。 你可以选择**编辑仪表板**修改默认的仪表板，或选择**新仪表板**创建自定义仪表板。 通过添加或删除磁贴来轻松自定义仪表板。 例如，若要添加计算磁贴，请选择**新建**。 右键单击**计算**，然后选择**固定到仪表板**。

## <a name="create-subscription-and-browse-available-resources"></a>创建订阅和浏览可用资源

如果没有订阅，则首先需要订阅某个套餐。 之后，可以浏览可用的资源。 若要浏览和创建资源，请使用以下任一方法：

- 选择**Marketplace**仪表板磁贴。
- 上**的所有资源**磁贴中，选择**创建资源**。
- 在左侧的导航窗格中，选择**新建**。

## <a name="learn-how-to-use-available-services"></a>了解如何使用可用服务

如需有关如何使用可用服务的指导，可以使用不同的选项。

- 你的组织或服务提供商可能提供了其自己的文档，如果他们提供了自定义的服务或应用，则通常是这种情况。
- 第三方应用提供自身的文档。
- 为保持服务的 Azure 一致性，我们强烈建议先查看 Azure Stack 文档。 若要访问 Azure 堆栈用户文档，请选择帮助图标，然后选择**帮助 + 支持**。

    ![帮助和支持在 UI 中的选项](media/azure-stack-use-portal/HelpAndSupport.png)

    具体而言，我们建议查看以下入门文章：

    - [重要注意事项：使用 Azure Stack 的服务或开发适用于 Azure Stack 的应用](azure-stack-considerations.md)
    - 在文档的“使用服务”部分中，针对每项服务都提供了注意事项文章。 “注意事项”页面描述了 Azure 中提供的服务与 Azure Stack 中提供的相同服务之间的差异。 有关示例，请参阅 [VM 注意事项](azure-stack-vm-considerations.md)。 “使用服务”部分中可能包含特定于 Azure Stack 的其他信息。

      可以使用 Azure 文档来大致了解服务，但必须注意这些差异。 请注意，“快速入门教程”磁贴中的文档链接指向 Azure 文档。

## <a name="get-support"></a>获取支持

如果需要支持，请联系你的组织或服务提供商以获取帮助。

如果使用 Azure Stack 开发工具包，则只能通过 [Azure Stack 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)获取支持。

## <a name="next-steps"></a>后续步骤

[重要注意事项：使用 Azure Stack 的服务或开发适用于 Azure Stack 的应用](azure-stack-considerations.md)
