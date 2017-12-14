---
title: "使用 Azure 堆栈门户 |Microsoft 文档"
description: "了解如何访问和使用 Azure 堆栈中的用户门户。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 5aa00123-5b87-45e0-a671-4165e66bfbc6
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 7c34d7a225be63da95f664525b0366ff89b28838
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="using-the-azure-stack-portal"></a>使用 Azure 堆栈门户

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

作为 Azure 堆栈服务的使用者，你可以使用 Azure 堆栈门户订阅公共产品，并使用可用于通过这些产品/服务的服务。 如果你已使用 Azure 门户之前，你已熟悉的用户界面。

## <a name="access-the-portal"></a>访问门户

你的 Azure 堆栈运算符 （服务提供商或你组织中的管理员），将让你知道正确的 URL 以访问门户。 

- 对于集成系统，URL 将取决于运算符的区域和外部的域名，并将采用格式 https://portal。&lt;*区域*&gt;。&lt;*FQDN*&gt;。
- 如果你使用 Azure 堆栈开发工具包，该门户的地址是 https://portal.local.azurestack.external。

![Azure 堆栈用户门户的屏幕截图](media/azure-stack-use-portal/UserPortal.png)

## <a name="customize-the-dashboard"></a>自定义仪表板

仪表板包含一组默认磁贴。 你可以单击**编辑仪表板**修改默认的仪表板，或单击**新仪表板**添加自定义仪表板。 你可以轻松添加到仪表板的磁贴。 例如，你可以单击**新建**，右键单击**计算**，然后单击**固定到仪表板**。

## <a name="create-subscription-and-browse-available-resources"></a>创建订阅并浏览可用资源
 
如果你还没有订阅，你需要执行操作的第一个操作是订阅产品。 之后，您可以浏览哪些资源将提供给你。 若要浏览并创建资源，请执行以下某项操作：

- 单击**Marketplace**仪表板磁贴。 
- 上**的所有资源**磁贴中，单击**创建资源**。
- 在左侧的导航窗格中，单击**新建**。

## <a name="learn-how-to-use-available-services"></a>了解如何使用可用的服务

如果你需要有关如何使用可用服务的指南，可能有不同的选项供你。

- 你的组织或服务提供商可能会提供其自己的文档。 这是如果它们提供自定义的服务或应用尤其如此。
- 第三方应用具有其自己的文档。
- 对于 Azure 一致的服务，我们强烈建议您首先阅读 Azure 堆栈文档。 若要访问 Azure 堆栈用户文档，请单击帮助图标，然后单击**帮助 + 支持**。
 
    ![UI 中的帮助和支持选项的屏幕截图](media/azure-stack-use-portal/HelpAndSupport.png)

    具体而言，我们建议你查看以下文章，若要开始：

    - [关键注意事项： 使用服务或 Azure 堆栈为构建应用](azure-stack-considerations.md)
    - 在"使用服务"部分中的文档，你将看到列出每个 Azure 一致的服务。 没有为每个服务，它描述了在 Azure 中，提供的服务和 Azure 堆栈中提供的相同服务之间的增量可以保持"注意事项"主题。 有关示例，请参阅[VM 注意事项](azure-stack-vm-considerations.md)。 可能是唯一的 Azure 堆栈的"使用服务"部分中的其他信息。 
     
      你可以使用 Azure 的文档与常规参考对于服务，但你必须注意这些区别。 了解文档链接上**快速入门教程**磁贴指向 Azure 文档。

## <a name="get-support"></a>获取支持

如果你需要其他支持，请联系你的组织或服务提供商，以获取帮助。 

如果你使用 Azure 堆栈开发工具包， [Azure 堆栈论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)是支持的唯一方法。

## <a name="next-steps"></a>后续步骤

[关键注意事项： 使用服务或 Azure 堆栈为构建应用](azure-stack-considerations.md)
