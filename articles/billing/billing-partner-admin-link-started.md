---
title: 将 Azure 帐户链接到合作伙伴 ID | Microsoft Docs
description: 通过将合作伙伴 ID 链接到用于管理客户资源的用户帐户来跟踪 Azure 客户的互动。
services: billing
author: dhirajgandhi
manager: dhgandhi
ms.author: banders
ms.date: 03/12/2019
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 0448ffbccddc913bd6359f5f6bbf42988239afb4
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706406"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>将合作伙伴 ID 链接到 Azure 帐户

Microsoft 合作伙伴提供的服务可帮助客户使用 Microsoft 产品实现业务和任务目标。 当代表客户管理、配置和支持 Azure 服务时, 合作伙伴用户将需要访问客户的环境。 合作伙伴可以使用合作伙伴管理链接将其合作伙伴网络 ID 与用于服务传递的凭据关联起来。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-access-from-your-customer"></a>从客户获取访问权限

在你链接合作伙伴 ID 之前，客户必须使用以下选项之一，授权你访问其 Azure 资源：

- **来宾用户**：客户可将你添加为来宾用户并分配任何基于角色的访问控制 (RBAC) 角色。 有关详细信息，请参阅[添加另一个目录中的来宾用户](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)。

- **目录帐户**：客户可以为你在其自己的目录中创建用户帐户，并分配任何 RBAC 角色。

- **服务主体**：客户可在其目录中为你的组织添加一个应用或脚本，并分配任何 RBAC 角色。 该应用或脚本的标识称为服务主体。

## <a name="link-to-a-partner-id"></a>链接到合作伙伴 ID

获取客户资源的访问权限后，请使用 Azure 门户、PowerShell 或 Azure CLI 将 Microsoft 合作伙伴网络 ID (MPN ID) 链接到用户 ID 或服务主体。 链接每个客户租户中的合作伙伴 ID。

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>使用 Azure 门户链接新合作伙伴 ID

1. 可以在 Azure 门户中[链接到合作伙伴 ID](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade)。

2. 登录到 Azure 门户。

3. 输入 Microsoft 合作伙伴 ID。 合作伙伴 ID 是组织的 [Microsoft 合作伙伴网络](https://partner.microsoft.com/) ID。

   ![显示链接到合作伙伴 ID 的屏幕截图](./media/billing-link-partner-id/link-partner-ID.PNG)

4. 若要链接另一个客户的合作伙伴 ID，请切换目录。 在“切换目录”下，选择你的目录。

   ![显示“切换”目录的屏幕截图](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>使用 PowerShell 链接到新合作伙伴 ID

1. 安装[ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) PowerShell 模块。

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


**如果我的公司有多个 MPN, 我应该使用哪个 ID？**

合作伙伴位置帐户和关联的 MPN Id 应该用于链接合作伙伴 ID。  了解有关[合作伙伴帐户](https://docs.microsoft.com/partner-center/account-structure)的详细信息

**在哪里可以找到受链接合作伙伴 ID 影响的收入报告？**

在["我的 Insights" 仪表板](https://partner.microsoft.com/membership/reports/myinsights)中, 合作伙伴中心提供云产品性能报告。 需要选择 "合作伙伴管理" 链接作为 "合作伙伴关联类型"。

**为什么我看不到报表中的客户？**

由于以下原因, 无法在报表中查看客户

1. 链接的用户帐户对任何客户 Azure 订阅或资源不具有[基于角色的访问权限](https://docs.microsoft.com/azure/role-based-access-control/overview)。

2. 用户具有[基于角色的访问](https://docs.microsoft.com/azure/role-based-access-control/overview)权限的 Azure 订阅没有任何使用权限。

**链接伙伴 ID 是否与 Azure Stack 一起使用？**

是的, 你可以链接 Azure Stack 的合作伙伴 ID。
