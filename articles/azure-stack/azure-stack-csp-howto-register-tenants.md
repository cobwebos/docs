---
title: "添加租户为使用量和计费到 Azure 堆栈 |Microsoft 文档"
description: "所需的步骤将最终用户添加到云服务提供商托管的 Azure 堆栈。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 67e5a67d7cd5caf6bd4d2625969b139411d62696
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>添加租户使用情况和计费到 Azure 堆栈

*适用于：Azure Stack 集成系统*

本指南介绍了所需的步骤将最终用户添加到托管的云服务提供程序 (CSP) 的 Azure 堆栈。 当新租户使用的资源时，Azure 堆栈会向其 CSP 订阅报告使用情况。

Csp 通常会向其部署，Azure 堆栈上的多个客户 （租户） 提供服务。 将租户添加到 Azure 堆栈注册可确保每个租户的使用情况将报告并对相应的 CSP 订阅计费。 如果未完成本文中的步骤，租户用量计费到 Azure 堆栈的初始注册中使用的订阅。 你可以将最终用户添加到 Azure 堆栈，来跟踪使用情况和管理其租户之前，你将需要将 Azure 堆栈配置为 CSP。 步骤和资源，请参阅[管理使用情况和计费 Azure 堆栈作为云服务提供商](azure-stack-add-manage-billing-as-a-csp.md)。

下图显示 CSP 需要遵循用于实现新的客户使用 Azure 堆栈，并设置跟踪客户的使用情况的步骤。 通过添加则最终用户，你将管理 Azure 堆栈中的资源。 可以用于管理其资源的两个选项：

1. 你可以保留最终客户租户，并向最终客户提供到本地 Azure 堆栈订阅的凭据。  
2. 或，则最终用户可以使用本地其订阅并将 CSP 添加为所有者权限的来宾。  

**若要添加的最终客户的步骤**

![来跟踪使用情况和管理的最终客户帐户中设置云服务提供程序](media\azure-stack-csp-enable-billing-usage-tracking\process-csp-enable-billing.png)

## <a name="create-a-new-customer-in-partner-center"></a>在合作伙伴中心中创建新客户

在合作伙伴中心中，为客户创建新的 Azure 订阅。 有关说明，请参阅[添加新客户](https://msdn.microsoft.com/partner-center/add-a-new-customer)。


##  <a name="create-an-azure-subscription-for-the-end-customer"></a>为最终客户创建 Azure 订阅

已在合作伙伴中心中创建的客户记录后，可以对目录中的产品的订阅对它们进行出售。 有关说明，请参阅[创建、 挂起或取消客户订阅](https://msdn.microsoft.com/partner-center/create-a-new-subscription)。

## <a name="create-a-guest-user-in-the-end-customer-directory"></a>在结束自定义目录中创建来宾用户

如果最终用户将能够管理其自己的帐户，在其目录中创建来宾用户，并将其发送信息。 最终用户将添加来宾和提升到来宾权限**所有者**到 Azure 堆栈 CSP 帐户。
 
## <a name="update-the-registration-with-the-end-customer-subscription"></a>更新注册到最终客户订阅

使用新客户的订阅更新你的注册。 Azure 报告使用从合作伙伴中心的客户标识客户的使用情况。 此步骤可确保每个客户的用法报告该客户的单个 CSP 订阅下。 这使得跟踪用户使用情况和计费容易得多。

> [!Note]  
> 若要执行此步骤，你必须[注册 Azure 堆栈](azure-stack-register.md)。

1. 使用提升的提示符下，打开 Windows PowerShell 并运行：  
    `Login-AzureRmAccount`
2. 键入你的 Azure 凭据。
3. 在 PowerShell 会话中，运行：

```powershell
    New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```
### <a name="new-azurermresource-powershell-parameters"></a>新 AzureRmResource PowerShell 参数
| 参数 | 说明 |
| --- | --- | 
|registrationSubscriptionID | 用于 Azure 堆栈在初始注册 Azure 订阅。 |
| customerSubscriptionID | 属于客户要注册 Azure 订阅 （而不是 Azure 堆栈）。 必须在 CSP 优惠; 中创建在实践中，这意味着通过合作伙伴中心。 如果客户有多个 Azure Active Directory 租户，则必须在将用于登录到 Azure 堆栈的租户中创建此订阅。
| resourceGroup | 在其中存储你的注册的 Azure 中的资源组。 
| registrationName | Azure 堆栈的注册的名称。 它是在 Azure 中存储的对象。 | 


> [!Note]  
> 租户需要使用它们使用每个 Azure 堆栈进行注册。 如果你有两个 Azure 堆栈部署，并且租户将使用这两个，你需要更新每个部署的初始注册信息与租户订阅。

## <a name="onboard-tenant-to-azure-stack"></a>上架到 Azure 堆栈的租户

配置 Azure 堆栈，以支持来自多个 Azure AD 租户，若要使用 Azure 堆栈中的服务的用户。 有关说明，请参阅[启用 Azure 堆栈中的多租户](azure-stack-enable-multitenancy.md)。


## <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>在 Azure 堆栈中的最终客户租户中创建本地资源

到 Azure 堆栈中添加新的客户或后端客户租户启用了你的来宾帐户所有者特权，验证可以在其租户中创建资源。 例如，他们能够[使用 Azure 堆栈门户创建 Windows 虚拟机](user\azure-stack-quick-windows-portal.md)。

## <a name="next-steps"></a>后续步骤

 - 若要查看的错误消息，如果在注册过程中触发它们的请参阅[租户注册错误消息](azure-stack-csp-ref-infrastructure.md#usage-and-billing-error-codes)。
 - 若要了解有关如何从 Azure 堆栈中检索资源使用情况信息的详细信息，请参阅[使用情况和 Azure 堆栈中的计费](/azure-stack-billing-and-chargeback.md)。
 - 若要查看如何对最终客户可能将你添加，为 CSP，作为其 Azure 堆栈的管理器，租户，请参阅[启用云服务提供程序，用于管理 Azure 堆栈订阅](user\azure-stack-csp-enable-billing-usage-tracking.md)。