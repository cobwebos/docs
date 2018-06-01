---
title: 使用 Windows VM 用户分配的 MSI 访问 Azure 资源管理器
description: 本教程介绍了使用 Windows VM 上用户分配的托管服务标识 (MSI) 访问 Azure 资源管理器的过程。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: arluca
ms.openlocfilehash: a2225409e4cb50d91c09207ee70b76df12925192
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301205"
---
# <a name="tutorial-use-a-user-assigned-managed-service-identity-msi-on-a-windows-vm-to-access-azure-resource-manager"></a>教程：使用 Windows VM 上用户分配的托管服务标识 (MSI) 访问 Azure 资源管理器

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

本教程介绍了如何创建用户分配的标识、将其分配给 Windows 虚拟机 (VM)，然后再使用此标识访问 Azure 资源管理器 API。 托管服务标识由 Azure 自动管理。 此标识可用于向支持 Azure AD 身份验证的服务进行身份验证，这样就无需在代码中嵌入凭据了。 

学习如何：

> [!div class="checklist"]
> * 创建 Windows VM 
> * 创建用户分配的标识
> * 将用户分配的标识分配给 Windows VM
> * 向用户分配标识授予对 Azure 资源管理器中资源组的访问权限 
> * 使用用户分配的标识获取访问令牌，并用它调用 Azure 资源管理器 
> * 读取资源组属性

## <a name="prerequisites"></a>先决条件

- 如果不熟悉托管服务标识，请查阅[概述](overview.md)部分。 请务必了解[系统分配标识与用户分配标识之间的差异](overview.md#how-does-it-work)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。
- 若要执行本教程中必需的资源创建和角色管理步骤，你的帐户需要在相应范围（订阅或资源组）具有“所有者”权限。 如果需要有关角色分配的帮助，请参阅[使用基于角色的访问控制管理对 Azure 订阅资源的访问权限](/azure/role-based-access-control/role-assignments-portal)。

如果选择在本地安装并使用 PowerShell，则本教程需要安装 Azure PowerShell 模块 5.7 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzureRmAccount` 以创建与 Azure 的连接。

## <a name="create-resource-group"></a>创建资源组

在以下示例中，在“EastUS”区域中创建了名为“myResourceGroupVM”的资源组。

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroupVM" -Location "EastUS"
```

## <a name="create-virtual-machine"></a>创建虚拟机

创建资源组后，创建 Windows VM。

使用 [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 设置虚拟机上管理员帐户所需的用户名和密码：

```azurepowershell-interactive
$cred = Get-Credential
```
使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 创建虚拟机。

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="create-a-user-assigned-identity"></a>创建用户分配的标识

用户分配的标识是作为独立的 Azure 资源创建的。 使用 [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity)，Azure 可在你的 Azure AD 租户中创建可分配给一个或多个 Azure 服务实例的标识。

> [!IMPORTANT]
> 创建用户分配标识时仅支持字母数字和连字符（0-9 或 a-z 或 A-Z 或 -）字符。 另外，分配给 VM/VMSS 的名称长度应限制为 24 个字符，否则它无法正常工作。 请关注后续更新。 有关详细信息，请参阅 [FAQ 和已知问题](known-issues.md)

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

该响应包含已创建的用户分配标识的详细信息，与以下示例类似。 记下用户分配标识的 `Id` 值，因为下一步会用到它：

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

## <a name="assign-the-user-assigned-identity-to-a-windows-vm"></a>将用户分配的标识分配给 Windows VM

用户分配的标识可以由多个 Azure 资源上的客户端使用。 使用以下命令将用户分配的标识分配给单个 VM。 将上一步返回的 `Id` 属性用于 `-IdentityID` 参数。

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzureRmVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>向用户分配的 MSI 授予对 Azure 资源管理器中资源组的访问权限 

托管服务标识 (MSI) 提供标识，你的代码可使用这些标识来请求访问令牌，以向支持 Azure AD 身份验证的资源 API 进行身份验证。 在本教程中，你的代码将访问 Azure 资源管理器 API。 

需先向标识授予对 Azure 资源管理器中资源的访问权限，代码才能访问 API。 在此情况下，即为包含 VM 的资源组。 根据环境适当地更新 `<SUBSCRIPTION ID>` 的值。

```azurepowershell-interactive
$spID = (Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

响应包含所创建的角色分配的详细信息，与以下示例类似：

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>使用 VM 标识获取访问令牌，并使用它调用资源管理器 

在本教程的剩余部分，你将从先前创建的 VM 入手。

1. 通过 [https://portal.azure.com](https://portal.azure.com) 登录到 Azure 门户

2. 在门户中，导航到“虚拟机”并转到 Windows 虚拟机，然后在“概述”中单击“连接”。

3. 输入创建 Windows VM 时使用的“用户名”和“密码”。

4. 现在，已经创建了与虚拟机的远程桌面连接，请在远程会话中打开 PowerShell。

5. 使用 PowerShell 的 `Invoke-WebRequest` 向本地 MSI 终结点发出请求，以获取 Azure 资源管理器的访问令牌。

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=73444643-8088-4d70-9532-c3a0fdc190fz&resource=https://management.azure.com' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

## <a name="read-the-properties-of-a-resource-group"></a>读取资源组属性

使用上一个步骤中检索到的访问令牌访问 Azure 资源管理器，并读取授予了用户分配标识访问权限的资源组的属性。 将 <SUBSCRIPTION ID> 替换为你环境的订阅 ID。

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
响应包含特定资源组信息，类似于下面的示例：

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/TestRG","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>后续步骤

- 有关 MSI 的概述，请参阅[托管服务标识概述](overview.md)。
