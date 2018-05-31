---
title: 创建区域冗余的 Azure 应用程序网关
description: 了解如何创建区域冗余的应用程序网关，此类网关不需要在每个区域中有一个单独的网关。
services: application-gateway
author: amsriva
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 5/8/2018
ms.author: victorh
ms.openlocfilehash: 03bc58db813534fdd17c9567f6425ab7357ed901
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937700"
---
# <a name="create-a-zone-redundant-azure-application-gateway---private-preview"></a>创建区域冗余的 Azure 应用程序网关 - 专用预览版

应用程序网关区域冗余平台是一个新的 SKU，该 SKU 在现有应用程序网关 SKU 的基础上进行了许多改进，其中包括：
- **区域复原** - 应用程序网关部署现在可以跨多个可用性区域，因此不需使用流量管理器在每个区域预配和固定单独的应用程序网关实例。 可以选择一个区域或多个区域来部署应用程序网关实例，以便确保区域故障复原。 应用程序的后端池可以通过类似方式分布在多个可用性区域中。
- **性能增强**
- **静态 VIP** - 应用程序网关 VIP 现在支持独占形式的静态 VIP 类型。 这样可确保与应用程序网关关联的 VIP 在重启后不会更改。
- **适用于客户 SSL 证书的密钥保管库集成**
- **缩短部署和更新时间**

> [!NOTE]
> 区域冗余应用程序网关目前为专用预览版。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="supported-regions"></a>支持的区域

目前，“美国东部 2”区域支持区域冗余应用程序网关。 很快会增加更多的区域。

## <a name="topologies"></a>拓扑

使用当前的版本时，不再需要创建区域固定的应用程序网关即可实现区域冗余。 同样的应用程序网关部署现在可以跨多个区域。

至少需要三个实例，这样才能确保将其分布到所有三个区域中。 添加更多实例时，应用程序网关会将实例分布到区域中。

以前的区域冗余拓扑如下图所示：

![旧的冗余拓扑](media/create-zone-redundant/old-redundant.png)

新的区域冗余拓扑如下图所示：

![新的区域冗余拓扑](media/create-zone-redundant/new-redundant.png)

## <a name="deployment"></a>部署

### <a name="prerequisites"></a>先决条件

目前，区域冗余功能仅提供专用预览版。 若要加入允许列表，必须向 appgwxzone@microsoft.com 发送电子邮件。 收到确认后，才能继续执行后续步骤。 请在申请加入允许列表的电子邮件中包括以下信息：

- 订阅 ID
- 区域名称
- 所需应用程序网关的大致数目

### <a name="resource-manager-template-deployment"></a>资源管理器模板部署

1. 确保所用订阅已加入允许列表，如前所述。
2. 收到确认以后，请登录到 Azure 帐户，然后选择适当的订阅（如果存在多个订阅）。 确保选择已加入允许列表的订阅。

   ```PowerShell
   Login-AzureRmAccount

   Select-AzureRmSubscription -Subscription "<whitelisted subscription name>”
   ```
3. 创建新的资源组

   ``` PowerShell
   New-AzureRmResourceGroup -Name <resource group name> -Location "East US 2"
   ```
4. 从 [GitHub](https://github.com/amitsriva/CrossZonePreview) 下载模板，记下用于保存模板的文件夹。
5. 在已创建的资源组中创建新的部署。 适当修改模板和参数文件，然后进行部署。 

   下图显示可以在 Azure 门户的何处检索租户 ID：

   ![门户中的租户 ID](media/create-zone-redundant/tenant-id.png)

此模板可创建以下资源：

- **用户分配的标识** - 此项用于允许应用程序网关实例访问密钥保管库并检索用户存储的证书。
- **KeyVault** - 用于存储用户证书的 Key Vault。 此项也可以是预先存在的 Key Vault。
- **机密** - 存储在 Key Vault 中的私钥。
- **访问策略** - 适用于 Key Vault 的访问策略，可以使用用户分配的标识来授权，这样应用程序网关部署就可以检索用户证书。
- **公共 IP 地址** - 用于访问应用程序网关的保留 IP 地址。 此 IP 地址在应用程序网关的生命周期中永不改变。
- **网络安全组** - 即在应用程序网关子网上自动创建的 NSG，可以在配置的侦听器上打开端口流量。 在新 SKU 中，这是显式创建和管理的，而在旧 SKU 中，此 NSG 是隐式的。
- **虚拟网络** - 用于部署应用程序网关和应用程序的 VNet。
- **应用程序网关** - 在所需区域中创建包含实例的应用程序网关。 默认选择所有区域（1、2、3）。 SKU 名称更改为 *Standard_v2*。 此 SKU 名称可以更改。 目前，自动缩放配置已将最小值和最大值设置为所需的实例数。 启用自动缩放以后，即可调整此项。

```PowerShell
New-AzureRmResourceGroupDeployment -Name Deployment1 -ResourceGroupName AmitVMSSLinuxTest9 -TemplateFile <complete path to template.json> -TemplateParameterFile <complete path to parameters.json>
```
### <a name="powershell-deployment"></a>PowerShell 部署

1. 确保所用订阅已加入允许列表，如前面的先决条件所述。
2. 从 [GitHub](https://github.com/amitsriva/CrossZonePreview/blob/master/Azure-Cmdlets-5.7.0.19009-x64.msi) 下载并安装专用 PowerShell MSI。
3. 从预览版注册确认电子邮件中提到的位置下载专用 PowerShell zip 文件。 将该文件解压缩到驱动器，然后记下位置。
4. 启用预览版以后，请先加载预览版模块，然后再登录到帐户：

   ```PowerShell
   $azurePSPath = "<complete path to Azure-PowerShell folder>"
   import-module "$azurePSPath\AzureRM.Profile\AzureRM.Profile.psd1"
   import-module "$azurePSPath\Azure.Storage\Azure.Storage.psd1"
   import-module "$azurePSPath\AzureRM.Resources\AzureRM.Resources.psd1"
   import-module "$azurePSPath\AzureRM.Network\AzureRM.Network.psd1"
   import-module "$azurePSPath\AzureRM.KeyVault\AzureRM.KeyVault.psd1"
   ```

4. 登录到 Azure 帐户，然后选择所需订阅（如果存在多个订阅）。 确保选择已加入允许列表的适当订阅。
5. 运行以下命令以建立公用常量，这些常量中的名称适用于大多数要创建的实体。 

   根据命名偏好视需要修改条目。

   ```PowerShell
   $location = "eastus2"
   $version = "300"

   $rgname = "RG_A_$version"
   $appgwName = "AGW_A_$version"
   $vaultName = "KVA$version"
   $userAssignedIdentityName = "UI_A_$version"
   $certificateName = "KVCA$version"
   $nsgName = "NSG_A_$version"
   $vnetName = "VN_A_$version"
   $gwSubnetName = "SN_A_$version"
   $gipconfigname = "GC_A_$version"
   $publicIpName = "PIP_A_$version"
   $fipconfig01Name = "FC_A_$version"
   $poolName = "BP_A_$version"
   $frontendPort01Name = "FP1_A_$version"
   $frontendPort02Name = "FP2_A_$version"
   $poolSetting01Name = "BS_A_$version"
   $listener01Name = "HL1_A_$version"
   $listener02Name = "HL2_A_$version"
   $rule01Name = "RR1_A_$version"
   $rule02Name = "RR2_A_$version"
   $AddressPrefix = "111.111.222.0" 
   ```
6. 创建资源组：

   ```PowerShell
   $resourceGroup = New-AzureRmResourceGroup -Name $rgname -Location $location -Force
   ```
7. 创建用户分配的标识，该标识用于访问应用程序网关，以便从 Key Vault 检索证书。

   ```PowerShell
   $userAssignedIdentity = New-AzureRmResource -ResourceGroupName $rgname -Location $location -ResourceName $userAssignedIdentityName -ResourceType Microsoft.ManagedIdentity/userAssignedIdentities -Force
   ```
8. 创建用于存储证书的 Key Vault：

   ```PowerShell
   $keyVault = New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgname -Location $location -EnableSoftDelete
   ```
9. 将证书作为机密上传到 Key Vault：

   ```PowerShell
   $securepfxpwd = ConvertTo-SecureString -String "<password>" -AsPlainText -Force

   $cert = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name         $certificateName -FilePath ‘<path to pfx file>'  -Password $securepfxpwd
   ```
10. 使用用户分配的标识将访问策略分配给 Key Vault。 这样就可以让应用程序网关实例访问 Key Vault 机密：

   ```PowerShell
   Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $rgname -PermissionsToSecrets get -ObjectId $userAssignedIdentity.Properties.principalId
   ```
11. 创建网络安全组 (NSG)，允许在创建新侦听器的端口上访问应用程序网关子网。

    例如，就 HTTP/HTTPS 来说，在默认端口上，NSG 会允许对 80、443 进行入站访问，而 65200-65535 则用于管理操作。

   ```PowerShell
   $srule01 = New-AzureRmNetworkSecurityRuleConfig -Name "listeners" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange 22,80,443 -Access Allow -Priority 100

   $srule02 = New-AzureRmNetworkSecurityRuleConfig -Name "managementPorts" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange "65200-65535" -Access Allow -Priority 101

   $nsg = New-AzureRmNetworkSecurityGroup -Name $nsgName -ResourceGroupName $rgname -Location $location -SecurityRules $srule01,$srule02 -Force
   ```

12. 创建 VNet 和子网：

   ```PowerShell
   $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 
   $gwSubnetName -AddressPrefix "$AddressPrefix/24" -NetworkSecurityGroup $nsg

   $vnet = New-AzureRmvirtualNetwork -Name $vnetName -ResourceGroupName $rgname -Location $location -AddressPrefix "$AddressPrefix/24" -Subnet $gwSubnet -Force
   ```
13. 创建类型为“已保留/静态”的公共 IP 地址：

   ```PowerShell
   $publicip = New-AzureRmPublicIpAddress -ResourceGroupName $rgname -name $publicIpName -location $location -AllocationMethod Static -Sku Standard -Force
   ```

14. 创建应用程序网关：

   ```PowerShell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name $gipconfigname -Subnet $gwSubnet

   $fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name $fipconfig01Name -PublicIPAddress $publicip

   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name $poolName -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

   $fp01 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort01Name -Port 443

   $fp02 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort02Name -Port 80

   $sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -KeyVaultSecretId $secret.Id

   $listener01 = New-AzureRmApplicationGatewayHttpListener -Name $listener01Name -Protocol Https -FrontendIPConfiguration
 $fipconfig01 -FrontendPort $fp01 -SslCertificate $sslCert01

   $listener02 = New-AzureRmApplicationGatewayHttpListener -Name $listener02Name -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp02

   $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name $poolSetting01Name -Port 80 -Protocol Http -CookieBasedAffinity Disabled

   $rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule01Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

   $rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule02Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener02 -BackendAddressPool $pool

   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2

   $listeners = @($listener02)

   $fps = @($fp01, $fp02)

   $fipconfigs = @($fipconfig01)

   $sslCerts = @($sslCert01)

   $rules = @($rule01, $rule02)

   $listeners = @($listener01, $listener02)

   $appgw = New-AzureRmApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Location $location -UserAssignedIdentityId $userAssignedIdentity.ResourceId -Probes $probeHttps -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfigs -FrontendPorts $fps -HttpListeners $listeners -RequestRoutingRules $rules -Sku $sku -SslPolicy $sslPolicy -sslCertificates $sslCerts -Force
   ```

15. 检索创建的应用程序网关的公共 IP 地址：

   ```PowerShell
   $pip = Get-AzureRmPublicIpAddress -Name $publicIpName -ResourceGroupName $rgname $pip.IpAddress
   ```

## <a name="frequently-asked-questions"></a>常见问题

-  应用程序网关预览版是否收费？

   预览版不收费。 应用程序网关之外的资源（例如 Key Vault、虚拟机等）会收费。
- 预览版在哪些区域推出？

   预览版目前已在“美国东部 2”区域推出。 很快会增加更多的区域。
- 预览版是否支持门户？

   否。预览版仅支持专用 PowerShell 模块和资源管理器模板。

- 预览版是否支持生产工作负荷？

   否。预览版不提供 SLA 或支持。 建议不要在预览版中加入生产工作负荷。 预览版的支持仅限使用电子邮件别名与产品组直接交互。

- 如何报告问题？

   专用预览版可能包含 Bug，因此可能需要进行频繁的代码部署。 请使用支持部门的电子邮件别名 appgwxzone@microsoft.com 来报告问题和获取帮助。

## <a name="known-issues-and-limitations"></a>已知问题和限制


|问题  |详细信息  |
|---------|---------|---------|
|计费     |目前不收费|
|诊断日志（不是指标）     |性能和请求/响应日志目前不显示|
|门户/CLI/SDK     |不对门户、CLI 或 SDK 提供支持。 不得使用门户向预览版网关发布更新。|
|通过模板进行更新有时会失败     |这是由于 KeyVault 访问策略的争用条件的缘故|Key Vault 和用户分配的标识在创建后即可从模板中删除，只需在模板中引用机密和标识。|
|自动缩放     |目前不支持自动缩放|
|WAF     |目前不支持 WAF|
|用户提供的证书和动态 VIP     |新模型不支持这些。 将 Key Vault 用于存储证书和静态 VIP。|
|旧版和预览版应用程序网关使用相同的子网     |带现成应用程序网关的子网（旧模型）不适用于专用预览版。|
|HTTP/2、FIPS 模式、WebSocket、Azure Web 应用（充当后端）     |目前不支持 |


## <a name="support-and-feedback"></a>支持和反馈

有关支持和反馈的问题，请联系 appgwxzone@microsoft.com。应用程序网关产品组很乐意听到你的反馈。我们会努力改进产品并根据你的需要提供指导。

## <a name="next-steps"></a>后续步骤

了解其他应用程序网关功能：

- [什么是 Azure 应用程序网关？](overview.md)