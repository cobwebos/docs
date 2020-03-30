---
title: 使用模板启用 Azure DS 域服务 |微软文档
description: 了解如何使用 Azure 资源管理器模板配置和启用 Azure 活动目录域服务
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: iainfou
ms.openlocfilehash: 2daadb539bc08df37f15c187866b735e45309288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612797"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板创建 Azure 活动目录域服务托管域

Azure Active Directory 域服务 (Azure AD DS) 提供与 Windows Server Active Directory 完全兼容的托管域服务，例如域加入、组策略、LDAP、Kerberos/NTLM 身份验证。 使用这些域服务就无需自行部署、管理和修补域控制器。 Azure AD DS 与现有的 Azure AD 租户集成。 这种集成可让用户使用其企业凭据登录，而你可以使用现有的组和用户帐户来保护对资源的访问。

本文介绍如何使用 Azure 资源管理器模板启用 Azure AD DS。 使用 Azure PowerShell 创建支持资源。

## <a name="prerequisites"></a>先决条件

要完成本文，您需要以下资源：

* 安装和配置 Azure PowerShell。
    * 如果需要，请按照说明[安装 Azure PowerShell 模块并连接到 Azure 订阅](/powershell/azure/install-az-ps)。
    * 请确保使用[连接-AzAccount][Connect-AzAccount] cmdlet 登录到 Azure 订阅。
* 安装和配置 Azure AD 电源外壳。
    * 如果需要，请按照说明[安装 Azure AD PowerShell 模块并连接到 Azure AD](/powershell/azure/active-directory/install-adv2)。
    * 请确保使用[连接 AzureAD][Connect-AzureAD] cmdlet 登录到 Azure AD 租户。
* 需要在 Azure AD 目录中拥有“全局管理员”特权才能启用 Azure AD DS。**
* 需要在 Azure 订阅中拥有“参与者”特权才能创建所需的 Azure AD DS 资源。**

## <a name="dns-naming-requirements"></a>DNS 命名要求

创建 Azure AD DS 实例时，请指定 DNS 名称。 选择此 DNS 名称时请注意以下事项：

* **内置域名：** 默认情况下，使用目录的内置域名 *（.onmicrosoft.com*后缀）。 若要启用通过 Internet 对托管域进行安全 LDAP 访问，则不能创建数字证书来保护与此默认域建立的连接。 Microsoft 拥有 *.onmicrosoft.com* 域，因此，证书颁发机构 (CA) 不会颁发证书。
* **自定义域名：** 最常见的方法是指定自定义域名，通常是您已经拥有且可路由的域名。 使用可路由的自定义域时，流量可根据需要正确传送，以支持你的应用程序。
* **不可路由域后缀：** 我们通常建议您避免使用不可路由的域名后缀，如*contoso.local*。 *.local* 后缀不可路由，并可能导致 DNS 解析出现问题。

> [!TIP]
> 如果创建自定义域名，请注意现有的 DNS 命名空间。 建议使用独立于任何现有 Azure 或本地 DNS 名称空间的域名。
>
> 例如，如果现有 dns 名称空间*为 contoso.com*，则创建具有*aaddscontoso.com*的自定义域名的 Azure AD DS 托管域。 如果需要使用安全 LDAP，则必须注册并拥有此自定义域名才能生成所需的证书。
>
> 您可能需要为环境中的其他服务创建一些其他 DNS 记录，或者在环境中的现有 DNS 名称空间之间创建条件 DNS 转发器。 例如，如果运行使用根 DNS 名称托管站点的 Web 服务器，则可能存在命名冲突，从而需要其他 DNS 条目。
>
> 在这些教程和操作操作文章中 *，aaddscontoso.com*的自定义域用作一个简短的示例。 在所有命令中，指定您自己的域名。

还存在以下 DNS 名称限制：

* **域前缀限制：** 不能创建前缀超过 15 个字符的托管域。 指定域名的前缀（如*aaddscontoso.com*域名中的*aaddscontoso）* 必须包含 15 个或更少字符。
* **网络名称冲突：** 托管域的 DNS 域名不应在虚拟网络中存在。 具体而言，请检查可能导致名称冲突的以下情况：
    * Azure 虚拟网络中是否已存在具有相同 DNS 域名的 Active Directory 域。
    * 计划在其中启用托管域的虚拟网络是否与本地网络建立了 VPN 连接。 在此方案中，确保在本地网络上没有具有相同 DNS 域名的域。
    * 虚拟网络中是否存在具有该名称的 Azure 云服务。

## <a name="create-required-azure-ad-resources"></a>创建所需的 Azure AD 资源

Azure AD DS 需要服务主体和 Azure AD 组。 这些资源允许 Azure AD DS 托管域同步数据，并定义哪些用户具有托管域中的管理权限。

首先，使用[注册-AzResource提供程序][Register-AzResourceProvider]cmdlet 注册 Azure AD 域服务资源提供程序：

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

使用[New-AzureADService 主体][New-AzureADServicePrincipal]cmdlet 创建 Azure AD 服务主体，以便 Azure AD DS 进行通信和身份验证。 特定应用程序 ID 使用名为*域控制器服务*，ID为*2565bd9d-da50-47d4-8b85-4c97f669dc36*。 不要更改此应用程序 ID。

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

现在，使用[新 AzureAD 组][New-AzureADGroup]cmdlet 创建名为*AAD DC 管理员的*Azure AD 组。 然后，向此组添加的用户被授予在 Azure AD DS 托管域上执行管理任务的权限。

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

创建*AAD DC 管理员*组后，使用[Add-AzureAdGroup 成员][Add-AzureADGroupMember]cmdlet 将用户添加到组。 您首先使用[Get-AzureADGroup][Get-AzureADGroup] cmdlet 获取*AAD DC 管理员*组对象 ID，然后使用[Get-AzureADUser][Get-AzureADUser] cmdlet 获取所需的用户的对象 ID。

在下面的示例中，具有 UPN 的`admin@aaddscontoso.onmicrosoft.com`帐户的用户对象 ID。 将此用户帐户替换为要添加到*AAD DC 管理员*组的用户的 UPN：

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@aaddscontoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

最后，使用[新 AzResourceGroup][New-AzResourceGroup] cmdlet 创建资源组。 在下面的示例中，资源组名为*myResourceGroup，* 并在*西部*区域创建。 使用您自己的名称和所需区域：

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

如果选择支持可用性区域的区域，则 Azure AD DS 资源会跨区域分布以实现额外的冗余。 可用性区域是 Azure 区域中独特的物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 为确保能够进行复原，所有已启用的区域中必须至少有三个单独的区域。

对于要跨区域分布 Azure AD DS，无需进行任何配置。 Azure 平台会自动处理资源的区域分配。 有关详细信息，请参阅区域可用性，请参阅 Azure[中有哪些可用性区域？][availability-zones]

## <a name="resource-definition-for-azure-ad-ds"></a>Azure AD DS 的资源定义

作为资源管理器资源定义的一部分，需要以下配置参数：

| 参数               | “值” |
|-------------------------|---------|
| domainName              | 托管域的 DNS 域名，考虑了以前有关命名前缀和冲突的要点。 |
| 过滤同步            | Azure AD DS 允许同步 Azure AD 中的所有用户和组，或者仅按范围同步特定的组。**** 如果选择同步所有用户和组，则以后无法选择仅执行按范围同步。<br /> 有关按范围同步的详细信息，请参阅 [Azure AD 域服务的按范围同步][scoped-sync]。|
| notificationSettings    | 如果在 Azure AD DS 托管域中生成任何警报，则可以发送电子邮件通知。 <br />可以为这些通知*启用*Azure 租户的*全局管理员*和*AAD DC 管理员*组的成员。<br /> 如果需要，当有需要注意的警报时，可以为通知添加其他收件人。|
| 域配置类型 | 默认情况下，Azure AD DS 托管域作为用户林创建**。 此类林可同步 Azure AD 中的所有对象，包括在本地 AD DS 环境中创建的所有用户帐户。 不需要指定*域配置*值来创建用户林。<br /> *资源*林仅同步直接在 Azure AD 中创建的用户和组。 资源林目前处于预览状态。 将值设置为*资源信任*以创建资源林。<br />有关资源林的详细信息，包括为何使用资源林以及如何创建本地 AD DS 域的林信任，请参阅 [Azure AD DS 资源林概述][resource-forests]**。|

以下压缩参数定义显示了如何声明这些值。 创建名为*aaddscontoso.com*的用户林，所有用户从 Azure AD 同步到 Azure AD DS 托管域：

```json
"parameters": {
    "domainName": {
        "value": "aaddscontoso.com"
    },
    "filteredSync": {
        "value": "Disabled"
    },
    "notificationSettings": {
        "value": {
            "notifyGlobalAdmins": "Enabled",
            "notifyDcAdmins": "Enabled",
            "additionalRecipients": []
        }
    },
    [...]
}
```

然后，以下压缩资源管理器模板资源类型用于定义和创建 Azure AD DS 托管域。 Azure 虚拟网络和子网必须已经存在，或者作为资源管理器模板的一部分创建。 Azure AD DS 托管域已连接到此子网。

```json
"resources": [
    {
        "apiVersion": "2017-06-01",
        "type": "Microsoft.AAD/DomainServices",
        "name": "[parameters('domainName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
            "domainName": "[parameters('domainName')]",
            "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
            "filteredSync": "[parameters('filteredSync')]",
            "notificationSettings": "[parameters('notificationSettings')]"
        }
    },
    [...]
]
```

这些参数和资源类型可用作更广泛的资源管理器模板的一部分来部署托管域，如下节所示。

## <a name="create-a-managed-domain-using-sample-template"></a>使用示例模板创建托管域

以下完整的资源管理器示例模板创建 Azure AD DS 托管域和支持的虚拟网络、子网和网络安全组规则。 网络安全组规则是保护托管域并确保流量能够正确流动所必需的。 将创建具有*aaddscontoso.com* DNS 名称的用户林，所有用户都从 Azure AD 同步：

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "apiVersion": {
            "value": "2017-06-01"
        },
        "domainConfigurationType": {
            "value": "FullySynced"
        },
        "domainName": {
            "value": "aaddscontoso.com"
        },
        "filteredSync": {
            "value": "Disabled"
        },
        "location": {
            "value": "westus"
        },
        "notificationSettings": {
            "value": {
                "notifyGlobalAdmins": "Enabled",
                "notifyDcAdmins": "Enabled",
                "additionalRecipients": []
            }
        },
        "subnetName": {
            "value": "aadds-subnet"
        },
        "vnetName": {
            "value": "aadds-vnet"
        },
        "vnetAddressPrefixes": {
            "value": [
                "10.1.0.0/24"
            ]
        },
        "subnetAddressPrefix": {
            "value": "10.1.0.0/24"
        },
        "nsgName": {
            "value": "aadds-nsg"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-06-01",
            "type": "Microsoft.AAD/DomainServices",
            "name": "[parameters('domainName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "domainName": "[parameters('domainName')]",
                "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
                "filteredSync": "[parameters('filteredSync')]",
                "domainConfigurationType": "[parameters('domainConfigurationType')]",
                "notificationSettings": "[parameters('notificationSettings')]"
            }
        },
        {
            "type": "Microsoft.Network/NetworkSecurityGroups",
            "name": "[parameters('nsgName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "AllowSyncWithAzureAD",
                        "properties": {
                            "access": "Allow",
                            "priority": 101,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "443"
                        }
                    },
                    {
                        "name": "AllowPSRemoting",
                        "properties": {
                            "access": "Allow",
                            "priority": 301,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "5986"
                        }
                    },
                    {
                        "name": "AllowRD",
                        "properties": {
                            "access": "Allow",
                            "priority": 201,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "CorpNetSaw",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "3389"
                        }
                    }
                ]
            },
            "apiVersion": "2018-04-01"
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('location')]",
            "apiVersion": "2018-04-01",
            "dependsOn": [
                "[concat('Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": "[parameters('vnetAddressPrefixes')]"
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetAddressPrefix')]",
                            "networkSecurityGroup": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
                            }
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {}
}
```

此模板可以使用首选部署方法（如[Azure 门户][portal-deploy][、Azure PowerShell][powershell-deploy]或 CI/CD 管道）进行部署。 下面的示例使用[新阿兹资源组部署][New-AzResourceGroupDeployment]cmdlet。 指定您自己的资源组名称和模板文件名：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
```

创建资源并将控件返回到 PowerShell 提示符需要几分钟时间。 Azure AD DS 托管域继续在后台预配，最多可能需要一个小时才能完成部署。 在 Azure 门户中，Azure AD DS 托管域的 **"概述"** 页显示整个部署阶段的当前状态。

当 Azure 门户显示 Azure AD DS 托管域已完成预配时，需要完成以下任务：

* 为虚拟网络更新 DNS 设置，以使虚拟机能够找到用于域加入或身份验证的托管域。
    * 要配置 DNS，请在门户中选择 Azure AD DS 托管域。 在 **"概述"** 窗口中，系统会提示您自动配置这些 DNS 设置。
* [将密码同步启用到 Azure AD 域服务](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)，以便最终用户可以使用其公司凭据登录到托管域。

## <a name="next-steps"></a>后续步骤

要查看 Azure AD DS 托管域的操作，可以[域加入 Windows VM、][windows-join][配置安全的 LDAP][tutorial-ldaps]和[配置密码哈希同步][tutorial-phs]。

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[availability-zones]: ../availability-zones/az-overview.md
[portal-deploy]: ../azure-resource-manager/templates/deploy-portal.md
[powershell-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[scoped-sync]: scoped-synchronization.md
[resource-forests]: concepts-resource-forest.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain
[New-AzResourceGroupDeployment]: /powershell/module/Az.Resources/New-AzResourceGroupDeployment
