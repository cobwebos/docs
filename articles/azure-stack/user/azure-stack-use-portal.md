---
title: 使用 Azure Stack 门户 | Microsoft Docs
description: 了解如何访问和使用 Azure Stack 中的用户门户。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 5aa00123-5b87-45e0-a671-4165e66bfbc6
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: mabrigg
ms.openlocfilehash: 279722cc53889cb0a261fcffde0c7e0f86be6dc5
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="using-the-azure-stack-portal"></a>使用 Azure Stack 门户

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

作为 Azure 堆栈服务的使用者，你可以使用 Azure 堆栈门户订阅公共产品，并使用这些服务将提供的服务。 如果你以前用过 Azure 门户，则很熟悉该用户界面。

## <a name="access-the-portal"></a>访问门户

Azure Stack 操作员（服务提供商或组织中的管理员）将会告知门户的正确 URL。

- 对于集成系统，URL 将取决于运算符的区域和外部域名称，和将采用格式https://portal。&lt;*区域*&gt;。&lt;*FQDN*&gt;。
- 如果使用 Azure Stack 开发工具包，则门户地址为 https://portal.local.azurestack.external。

![Azure Stack 用户门户的屏幕截图](media/azure-stack-use-portal/UserPortal.png)

## <a name="customize-the-dashboard"></a>自定义仪表板

仪表板包含一组默认磁贴。 你可以单击**编辑仪表板**修改默认的仪表板，或单击**新仪表板**创建自定义仪表板。 通过添加或删除磁贴，你可以轻松自定义仪表板。 例如，若要添加计算磁贴，请单击**新建**。 右键单击**计算**，然后单击**固定到仪表板**。

## <a name="create-subscription-and-browse-available-resources"></a>创建订阅和浏览可用资源
 
如果没有订阅，则首先需要订阅某个产品。 之后，您可以浏览可用的资源。 若要浏览并创建资源，可以使用任何以下方法之一：

- 单击仪表板上的“Marketplace”磁贴。
- 在“所有资源”磁贴上，单击“创建资源”。
- 在左侧导航窗格上，单击“新建”。

## <a name="learn-how-to-use-available-services"></a>了解如何使用可用服务

如需有关如何使用可用服务的指导，可以使用不同的选项。

- 你的组织或服务提供商可能会提供其自己的文档，通常是这种情况，如果他们提供自定义的服务或应用程序。
- 第三方应用提供自身的文档。
- 为保持服务的 Azure 一致性，我们强烈建议先查看 Azure Stack 文档。 若要访问 Azure Stack 用户文档，请单击“帮助”图标，然后单击“帮助 + 支持”。
 
    ![UI 中“帮助 + 支持”选项的屏幕截图](media/azure-stack-use-portal/HelpAndSupport.png)

    具体而言，我们建议查看以下入门文章：

    - [重要注意事项：使用 Azure Stack 的服务或开发适用于 Azure Stack 的应用](azure-stack-considerations.md)
    - 在"使用服务"部分中的文档，没有为每个服务的"注意事项"文章。 "注意事项"页描述在 Azure 中，提供的服务和 Azure 堆栈中提供的相同服务之间的差异。 有关示例，请参阅 [VM 注意事项](azure-stack-vm-considerations.md)。 “使用服务”部分可能包含特定于 Azure Stack 的其他信息。
     
      可以使用 Azure 文档来大致了解服务，但必须注意这些差异。 请注意，“快速入门教程”磁贴中的文档链接指向 Azure 文档。

## <a name="get-support"></a>获取支持

如果你需要其他支持，请与你的组织或服务提供商联系以获取帮助。

如果你使用 Azure 堆栈开发工具包， [Azure 堆栈论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)是支持的唯一源。

## <a name="next-steps"></a>后续步骤

[重要注意事项：使用 Azure Stack 的服务或开发适用于 Azure Stack 的应用](azure-stack-considerations.md)
