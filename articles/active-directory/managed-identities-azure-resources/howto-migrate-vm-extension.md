---
title: 停止使用托管标识 VM 扩展-Azure AD
description: 有关停止使用 VM 扩展并开始使用 Azure 实例元数据服务 (IMDS) 进行身份验证的分步说明。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/25/2018
ms.author: markvi
ms.openlocfilehash: 3440713c287967655678e1cde2c000a6ed28b900
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74183951"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>如何停止使用虚拟机托管标识扩展并开始使用 Azure 实例元数据服务

## <a name="virtual-machine-extension-for-managed-identities"></a>托管标识的虚拟机扩展

托管标识的虚拟机扩展用于请求虚拟机中托管标识的令牌。 工作流包括以下步骤：

1. 首先，资源内的工作负荷调用本地终结点 `http://localhost/oauth2/token` 以请求访问令牌。
2. 然后，虚拟机扩展使用托管标识的凭据从 Azure AD 请求访问令牌。 
3. 访问令牌将返回到调用方，可用于对支持 Azure AD 身份验证的服务（例如 Azure Key Vault 或 Azure 存储）进行身份验证。

由于下一部分所述的多种限制，托管标识 VM 扩展已弃用，用户可以改用 Azure 实例元数据服务 (IMDS) 中的等效终结点

### <a name="provision-the-extension"></a>预配扩展 

将虚拟机或虚拟机规模集配置为使用托管标识时，可以选择性地在 `-Type`Set-AzVMExtension[ cmdlet 中使用 ](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) 参数，来预配 Azure 资源托管标识 VM 扩展。 可以传递 `ManagedIdentityExtensionForWindows` 或 `ManagedIdentityExtensionForLinux`（取决于虚拟机的类型），并使用 `-Name` 参数将其命名。 `-Settings` 参数指定 OAuth 令牌终结点用于令牌获取的端口：

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

还可以使用 Azure 资源管理器部署模板预配 VM 扩展，方法是将以下 JSON 添加到模板中的 `resources` 节（使用 `ManagedIdentityExtensionForLinux` 指定名称，使用 type 元素指定 Linux 版本）。

    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2018-06-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    }
    ```
    
    
如果使用虚拟机规模集，则还可以使用 [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) cmdlet 来预配 Azure 资源托管标识虚拟机规模集扩展。 可以传递 `ManagedIdentityExtensionForWindows` 或 `ManagedIdentityExtensionForLinux`（取决于虚拟机规模集的类型），并使用 `-Name` 参数将其命名。 `-Settings` 参数指定 OAuth 令牌终结点用于令牌获取的端口：

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
若要使用 Azure 资源管理器部署模板预配虚拟机规模集扩展，请将以下 JSON 添加到模板中的 `extensionpProfile` 节（使用 `ManagedIdentityExtensionForLinux` 指定名称，使用 type 元素指定 Linux 版本）。

    ```json
    "extensionProfile": {
        "extensions": [
            {
                "name": "ManagedIdentityWindowsExtension",
                "properties": {
                    "publisher": "Microsoft.ManagedIdentity",
                    "type": "ManagedIdentityExtensionForWindows",
                    "typeHandlerVersion": "1.0",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                        "port": 50342
                    },
                    "protectedSettings": {}
                }
            }
    ```

由于 DNS 查找失败，虚拟机扩展的预配可能会失败。 如果发生这种情况，请重启虚拟机，然后重试。 

### <a name="remove-the-extension"></a>删除扩展 
若要删除扩展，请在 Azure CLI 中结合 `-n ManagedIdentityExtensionForWindows`az vm extension delete`-n ManagedIdentityExtensionForLinux` 或 [az vmss extension delete](https://docs.microsoft.com/cli/azure/vm/)（针对虚拟机规模集）使用 [ 或 ](https://docs.microsoft.com/cli/azure/vmss) 开关，或者在 Powershell 中使用 `Remove-AzVMExtension`：

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>使用虚拟机扩展获取令牌

下面是使用 Azure 资源托管标识 VM 扩展终结点的示例请求：

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| 元素 | 说明 |
| ------- | ----------- |
| `GET` | HTTP 谓词，指示想要从终结点检索数据。 在本例中，该数据为 OAuth 访问令牌。 | 
| `http://localhost:50342/oauth2/token` | Azure 资源的托管标识终结点，其中 50342 是可配置的默认端口。 |
| `resource` | 一个查询字符串参数，表示目标资源的应用 ID URI。 它也会显示在所颁发令牌的 `aud`（受众）声明中。 本示例请求一个用于访问 Azure 资源管理器的、应用 ID URI 为 https://management.azure.com/ 的令牌。 |
| `Metadata` | 一个 HTTP 请求标头字段，Azure 资源的托管标识需要使用该元素来缓解服务器端请求伪造 (SSRF) 攻击。 必须将此值设置为“true”（全小写）。|
| `object_id` | （可选）一个查询字符串参数，指示要将此令牌用于的托管标识的 object_id。 如果 VM 有用户分配的多个托管标识，则为必需的。|
| `client_id` | （可选）一个查询字符串参数，指示要将此令牌用于的托管标识的 client_id。 如果 VM 有用户分配的多个托管标识，则为必需的。|


示例响应：

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| 元素 | 说明 |
| ------- | ----------- |
| `access_token` | 请求的访问令牌。 调用受保护 REST API 时，该令牌将作为“持有者”令牌嵌入在 `Authorization` 请求标头字段中，使 API 能够对调用方进行身份验证。 | 
| `refresh_token` | 未由 Azure 资源的托管标识使用。 |
| `expires_in` | 访问令牌在过期之前保持有效的秒数，从颁发时间开始算起。 可在令牌的 `iat` 声明中找到颁发时间。 |
| `expires_on` | 访问令牌过期的时间范围。 该日期表示为自“1970-01-01T0:0:0Z UTC”开始的秒数（对应于令牌的 `exp` 声明）。 |
| `not_before` | 访问令牌生效且可被接受的时间范围。 该日期表示为自“1970-01-01T0:0:0Z UTC”开始的秒数（对应于令牌的 `nbf` 声明）。 |
| `resource` | 请求访问令牌时所针对的资源，与请求的 `resource` 查询字符串参数匹配。 |
| `token_type` | 令牌的类型，这是一个“持有者”访问令牌，意味着资源可向此令牌的持有者授予访问权限。 |


### <a name="troubleshoot-the-virtual-machine-extension"></a>排查虚拟机扩展问题 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>失败后重启虚拟机扩展

在 Windows 和某些 Linux 版本中，如果该扩展停止，可使用以下 cmdlet 手动重启该扩展：

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

其中： 
- 适用于 Windows 的扩展名称和类型是：`ManagedIdentityExtensionForWindows`
- 适用于 Linux 的扩展名称和类型是：`ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>尝试 Azure 资源托管标识扩展的架构导出功能时，“自动化脚本”失败

如果在虚拟机上启用了 Azure 资源托管标识，当尝试将“自动化脚本”功能用于虚拟机或其资源组时，将显示以下错误：

![Azure 资源托管标识自动化脚本导出错误](./media/howto-migrate-vm-extension/automation-script-export-error.png)

Azure 资源托管标识虚拟机扩展当前不支持将其架构导出到资源组模板的功能。 因此，生成的模板不显示用于在资源上启用 Azure 资源托管标识的配置参数。 按照[使用模板在 Azure 虚拟机上配置 Azure 资源托管标识](qs-configure-template-windows-vm.md)中的示例，可以手动添加这些部分。

当架构导出功能可用于 Azure 资源托管标识虚拟机扩展（计划在 2019 年 1 月弃用）时，它将在[导出包含 VM 扩展的资源组](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)中列出。

## <a name="limitations-of-the-virtual-machine-extension"></a>虚拟机扩展的限制 

使用虚拟机扩展时存在几项主要限制。 

 * 最严重的限制是，用于请求令牌的凭据存储在虚拟机上。 成功侵入虚拟机的攻击者可能会盗取凭据。 
 * 此外，有多个 Linux 分发版仍不支持虚拟机扩展，但这些分发版上修改、生成和测试该扩展需要投入巨大的开发成本。 目前仅支持以下 Linux 分发版： 
    * CoreOS Stable
    * CentOS 7.1 
    * Red Hat 7.2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * 使用托管标识部署虚拟机会对性能产生影响，因为还需要预配虚拟机扩展。 
 * 最后，对于每个虚拟机，虚拟机扩展只能支持用户分配的 32 个托管标识。 

## <a name="azure-instance-metadata-service"></a>Azure 实例元数据服务

[Azure 实例元数据服务 (IMDS)](/azure/virtual-machines/windows/instance-metadata-service) 是一个 REST 终结点，提供有关可用于管理和配置虚拟机的正在运行的虚拟机实例的信息。 该终结点位于已知不可路由的 IP 地址 (`169.254.169.254`)，该地址只能从虚拟机中访问。

使用 Azure IMDS 请求令牌可获得多种优势。 

1. 服务位于虚拟机的外部，因此托管标识使用的凭据不再保留在虚拟机上。 它们托管在 Azure 虚拟机的主机上并受到保护。   
2. Azure IaaS 支持的所有 Windows 和 Linux 操作系统都可以使用托管标识。
3. 部署速度更快且更简单，因为不再需要预配 VM 扩展。
4. 使用 IMDS 终结点时，最多可将用户分配的 1000 个托管标识分配到单个虚拟机。
5. 与使用虚拟机扩展的请求相比，使用 IMDS 的请求没有明显的变化，因此，可以十分方便地在当前使用虚拟机扩展的现有部署基础上进行移植。

由于这些原因，一旦弃用虚拟机扩展，Azure IMDS 服务就会成为请求令牌的不二方法。 


## <a name="next-steps"></a>后续步骤

* [如何在 Azure 虚拟机上使用 Azure 资源的托管标识获取访问令牌](how-to-use-vm-token.md)
* [Azure 实例元数据服务](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)
