---
title: 将 Azure 帐户链接到合作伙伴 ID | Microsoft Docs
description: 通过将合作伙伴 ID 链接到用于管理客户资源的用户帐户来跟踪 Azure 客户的互动。
services: billing
author: dhirajgandhi
ms.author: dhgandhi
ms.date: 03/12/2018
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: abab0e63f91ad34d2671c37773d47c31eeeb8339
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="link-partner-id-to-your-azure-accounts"></a>将合作伙伴 ID 链接到 Azure 帐户 
合作伙伴可以通过将合作伙伴 ID 链接到用于管理客户资源的帐户，来跟踪自己在客户互动中的影响度。

此功能目前以公共预览版提供。 

## <a name="get-access-from-your-customer"></a>从客户获取访问权限 
在你链接合作伙伴 ID 之前，客户必须使用以下选项之一，授权你访问其 Azure 资源：

- **来宾用户：**客户可将你添加为来宾用户并分配任何 RBAC 角色。 有关详细信息，请参阅[添加另一个目录中的来宾用户](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)。

- **目录帐户：**客户可在其目录中为你的组织创建一个新用户，并分配任何 RBAC 角色。 

- **服务主体：**客户可在其目录中为你的组织添加一个应用或脚本，并分配任何 RBAC 角色。 该应用或脚本的标识称为服务主体。

## <a name="link-partner-id"></a>链接合作伙伴 ID
获取客户资源的访问权限后，使用 PowerShell 或 CLI 将 Microsoft 合作伙伴网络 ID (MPN ID) 链接到用户 ID 或服务主体。 必须为每个客户租户链接合作伙伴 ID。 

### <a name="use-powershell-to-link-new-partner-id"></a>使用 PowerShell 链接新合作伙伴 ID

1. 安装 [AzurePartnerRP](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner/0.1.0-preview) Powershell 模块。

2. 使用用户帐户或服务主体登录到客户的租户。有关详细信息，请参阅[使用 Powershell 登录](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-5.2.0)。
 
   ```azurepowershell-interactive
    C:\> Connect-AzureRmAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
   ```


3. 链接新合作伙伴 ID。 合作伙伴 ID 是组织的 [Microsoft 合作伙伴网络 (MPN)](https://partner.microsoft.com/) ID。

    ```azurepowershell-interactive
    C:\> new-AzureRmManagementPartner -PartnerId 12345 
    ```

#### <a name="get-the-linked-partner-id"></a>获取链接的合作伙伴 ID
```azurepowershell-interactive
C:\> get-AzureRmManagementPartner 
```

#### <a name="update-the-linked-partner-id"></a>更新链接的合作伙伴 ID
```azurepowershell-interactive
C:\> Update-AzureRmManagementPartner -PartnerId 12345 
```
#### <a name="delete-the-linked-partner-id"></a>删除链接的合作伙伴 ID
```azurepowershell-interactive
C:\> remove-AzureRmManagementPartner -PartnerId 12345 
```

### <a name="use-cli-to-link-new-partner-id"></a>使用 CLI 链接新合作伙伴 ID
1.  安装 CLI 扩展。

    ```azure-cli
    C:\ az extension add --name managementpartner
    ``` 

2.  使用用户帐户或服务主体登录到客户的租户。 有关详细信息，请参阅[使用 Azure CLI 2.0 登录](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。

    ```azure-cli
    C:\ az login --tenant <tenant>
    ``` 


3.  链接新合作伙伴 ID。 合作伙伴 ID 是组织的 [Microsoft 合作伙伴网络 (MPN)](https://partner.microsoft.com/) ID。

     ```azure-cli
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>获取链接的合作伙伴 ID
```azure-cli
C:\ az managementpartner show
``` 

#### <a name="update-the-linked-partner-id"></a>更新链接的合作伙伴 ID
```azure-cli
C:\ az managementpartner update --partner-id 12345
``` 

#### <a name="delete-the-linked-partner-id"></a>删除链接的合作伙伴 ID
```azure-cli
C:\ az managementpartner delete --partner-id 12345
``` 


## <a name="frequently-asked-questions"></a>常见问题

**谁可以链接合作伙伴 ID？**

合作伙伴组织中管理客户资源的任意用户都可将合作伙伴 ID 链接到帐户。
  

**链接合作伙伴 ID 后，是否可以更改该 ID？**

是的，可以更改、添加或删除已链接的合作伙伴 ID。

**如果某个用户在多个客户租户中具有帐户怎么办？**

合作伙伴 ID 与帐户之间的链接是针对每个客户租户执行的。  必须链接每个客户租户中的合作伙伴 ID。

**其他合作伙伴或客户是否可以编辑或删除合作伙伴 ID 的链接？**

该链接是在帐户级别关联的。 只有你可以编辑或删除合作伙伴 ID 的链接。 客户和其他合作伙伴无法更改合作伙伴 ID 的链接。 


