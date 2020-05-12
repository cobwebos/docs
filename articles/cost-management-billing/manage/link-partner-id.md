---
title: 将 Azure 帐户链接到合作伙伴 ID
description: 通过将合作伙伴 ID 链接到用于管理客户资源的用户帐户来跟踪 Azure 客户的互动。
author: dhirajgandhi
ms.reviewer: dhgandhi
ms.author: banders
ms.date: 05/04/2020
ms.service: cost-management-billing
ms.topic: conceptual
ms.openlocfilehash: f0d45c9dd8dc33226ca75fe34467a8695c8aae4d
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82778815"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>将合作伙伴 ID 链接到 Azure 帐户

Microsoft 合作伙伴提供的服务可帮助客户使用 Microsoft 产品实现业务和任务目标。 当代表客户管理、配置和支持 Azure 服务时，合作伙伴用户将需要访问客户的环境。 合作伙伴可以使用合作伙伴管理链接 (PAL) 将其合作伙伴网络 ID 与用于服务交付的凭据相关联。

PAL 使 Microsoft 能够识别哪些合作伙伴促成了 Azure 客户的成功。Microsoft 可以根据帐户的权限（RBAC 角色）和范围（订阅、资源组、资源），将影响力和 Azure 消费收入归结于组织。

## <a name="get-access-from-your-customer"></a>从客户获取访问权限

在你链接合作伙伴 ID 之前，客户必须使用以下选项之一，授权你访问其 Azure 资源：

- **来宾用户**：客户可将你添加为来宾用户并分配任何基于角色的访问控制 (RBAC) 角色。 有关详细信息，请参阅[添加另一个目录中的来宾用户](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)。

- **目录帐户**：客户可以为你在其自己的目录中创建用户帐户，并分配任何 RBAC 角色。

- **服务主体**：客户可在其目录中为你的组织添加一个应用或脚本，并分配任何 RBAC 角色。 该应用或脚本的标识称为服务主体。

- **Azure Lighthouse**：你的客户可以委托一个订阅（或资源组），这样你的用户就可以通过你的租户在其上工作。 有关详细信息，请参阅 [Azure 委托资源管理](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)。

## <a name="link-to-a-partner-id"></a>链接到合作伙伴 ID

获取客户资源的访问权限后，请使用 Azure 门户、PowerShell 或 Azure CLI 将 Microsoft 合作伙伴网络 ID (MPN ID) 链接到用户 ID 或服务主体。 链接每个客户租户中的合作伙伴 ID。

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>使用 Azure 门户链接新合作伙伴 ID

1. 可以在 Azure 门户中[链接到合作伙伴 ID](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade)。

2. 登录到 Azure 门户。  

3. 输入 Microsoft 合作伙伴 ID。 合作伙伴 ID 是组织的 [Microsoft 合作伙伴网络](https://partner.microsoft.com/) ID。

   ![显示链接到合作伙伴 ID 的屏幕截图](./media/link-partner-id/link-partner-id01.png)

4. 若要链接另一个客户的合作伙伴 ID，请切换目录。 在“切换目录”下，选择你的目录  。

   ![显示“切换”目录的屏幕截图](./media/link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>使用 PowerShell 链接到新合作伙伴 ID

1. 安装 [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) PowerShell 模块。

2. 使用用户帐户或服务主体登录到客户的租户。 有关详细信息，请参阅[使用 PowerShell 登录](https://docs.microsoft.com/powershell/azure/authenticate-azureps)。

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. 链接到新合作伙伴 ID。 合作伙伴 ID 是组织的 [Microsoft 合作伙伴网络](https://partner.microsoft.com/) ID。

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>获取链接的合作伙伴 ID
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>更新链接的合作伙伴 ID
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>删除链接的合作伙伴 ID
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>使用 Azure CLI 链接到新合作伙伴 ID
1. 安装 Azure CLI 扩展。

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. 使用用户帐户或服务主体登录到客户的租户。 有关详细信息，请参阅[使用 Azure CLI 登录](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. 链接到新合作伙伴 ID。 合作伙伴 ID 是组织的 [Microsoft 合作伙伴网络](https://partner.microsoft.com/) ID。

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>获取链接的合作伙伴 ID
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>更新链接的合作伙伴 ID
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>删除链接的合作伙伴 ID
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>后续步骤

加入 [Microsoft 合作伙伴社区](https://aka.ms/PALdiscussion)中的讨论，以接收更新或发送反馈。

## <a name="frequently-asked-questions"></a>常见问题

**谁可以链接合作伙伴 ID？**

合作伙伴组织中管理客户的 Azure 资源的任何用户都可将合作伙伴 ID 链接到帐户。

**链接合作伙伴 ID 后，是否可以更改？**

是的。 可以更改、添加或删除已链接的合作伙伴 ID。

**如果某个用户在多个客户租户中具有帐户怎么办？**

合作伙伴 ID 与帐户之间的链接是针对每个客户租户执行的。 链接每个客户租户中的合作伙伴 ID。

**其他合作伙伴或客户是否可以编辑或删除合作伙伴 ID 的链接？**

该链接在用户帐户级别关联。 只有你可以编辑或删除合作伙伴 ID 的链接。 客户和其他合作伙伴无法更改合作伙伴 ID 的链接。


**如果我的公司有多个 MPN ID，我应该使用哪个 MPN ID？**

合作伙伴位置帐户和关联的 MPN ID 应该用于链接合作伙伴 ID。  详细了解[合作伙伴帐户](https://docs.microsoft.com/partner-center/account-structure)

**在哪里可以找到受关联合作伙伴 ID 影响的收入报告？**

合作伙伴中心内的合作伙伴可以在[“我的见解”仪表板](https://partner.microsoft.com/membership/reports/myinsights)上找到云产品性能报告。 需要选择“合作伙伴管理链接”作为合作伙伴关联类型。

**为什么我在报告中看不到我的客户？**

由于以下原因，无法在报告中看到客户

1. 链接的用户帐户对任何客户 Azure 订阅或资源都没有[基于角色的访问权限](https://docs.microsoft.com/azure/role-based-access-control/overview)。

2. 用户对其具有[基于角色的访问权限](https://docs.microsoft.com/azure/role-based-access-control/overview)的 Azure 订阅没有任何使用情况。

**链接合作伙伴 ID 是否适用于 Azure Stack？**

适用，可以为 Azure Stack 链接合作伙伴 ID。

**如果我的公司使用 [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) 访问客户资源，则如何关联我的合作伙伴 ID？**

如果[将托管服务套餐发布到 Azure Marketplace](https://docs.microsoft.com/azure/lighthouse/how-to/publish-managed-services-offers)，以便将客户加入 Azure 委托的资源管理，则 MPN ID 会自动进行关联。 如果[通过部署 Azure 资源管理器模板来加入客户](https://docs.microsoft.com/azure/lighthouse/how-to/onboard-customer)，则需将 Microsoft 合作伙伴网络 (MPN) ID 与至少一个可访问每个载入的订阅的用户帐户相关联。 请注意，你需要在服务提供商租户中这样做。 简单起见，我们建议在租户中创建一个与你的 MPN ID 相关联的服务主体帐户，并授予它对你载入的每个客户的“读者”访问权限。 在此示例中，我们使用了“RBAC 读者”角色，它是没资格获得“合作伙伴赚取的返点”的角色之一。 有关角色的更多信息，请参阅[与合作伙伴赚取的返点相关的角色和权限](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3QuW2)。

