---
title: 从 v1 迁移到 v2 - Azure 应用程序网关
description: 本文介绍如何将 Azure 应用程序网关和 Web 应用程序防火墙从 v1 迁移到 v2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 57a49f9e1473f33eceba14591815415338aeecf4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198804"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>将 Azure 应用程序网关和 Web 应用程序防火墙从 v1 迁移到 v2

[Azure 应用程序网关和 Web 应用程序防火墙 (WAF) v2](application-gateway-autoscaling-zone-redundant.md) 现已推出，它提供自动缩放和可用性区域冗余等附加功能。 但是，现有 v1 网关不会自动升级到 v2。 若要从 v1 迁移到 v2，请遵循本文中的步骤。

迁移分为两个阶段：

1. 迁移配置
2. 迁移客户端流量

本文介绍配置迁移。 客户端流量迁移过程因环境而异。 不过，本文提供了一些概要性的普通[建议](#migrate-client-traffic)。

## <a name="migration-overview"></a>迁移概述

我们提供了一个用于执行以下操作的 Azure PowerShell 脚本：

* 在指定的虚拟网络子网中创建新的 Standard_v2 或 WAF_v2 网关。
* 将与 v1 Standard 或 WAF 网关关联的配置无缝复制到新建的 Standard_V2 或 WAF_V2 网关。

### <a name="caveatslimitations"></a>注意事项/限制

* 新的 v2 网关使用新的公共和专用 IP 地址。 无法将与现有 v1 网关关联的 IP 地址无缝移动到 v2。 但是，可将现有的（未分配的）公共或专用 IP 地址分配到新的 v2 网关。
* 必须为 v1 网关所在的虚拟网络中的另一个子网提供 IP 地址空间。 该脚本无法在已有 v1 网关的任何现有子网中创建 v2 网关。 但是，如果现有子网已包含 v2 网关，只要该子网具有足够的 IP 地址空间，它就仍可正常运行。
* 若要迁移 TLS/SSL 配置，必须指定 v1 网关中使用的所有 TLS/SSL 证书。
* 如果为 v1 网关启用了 FIPS 模式，该网关不会迁移到新的 v2 网关。 v2 不支持 FIPS 模式。
* v2 不支持 IPv6，因此不会迁移启用了 IPv6 的 v1 网关。 如果运行该脚本，它可能不会完成。
* 如果 v1 网关只有专用 IP 地址，该脚本将为新的 v2 网关创建一个公共 IP 地址和一个专用 IP 地址。 v2 网关目前不支持仅指定专用 IP 地址。
* 名称中包含除字母、数字、连字符和下划线以外的任何内容的标头不会传递给你的应用程序。 这仅适用于标头名称而不是标头值。 与 v1 相比，这是一个中断性变更。

## <a name="download-the-script"></a>下载脚本

从 [PowerShell 库](https://www.powershellgallery.com/packages/AzureAppGWMigration)下载迁移脚本。

## <a name="use-the-script"></a>使用脚本

根据本地 PowerShell 环境的设置和首选项，可以使用两个选项：

* 如果你尚未安装 Azure Az 模块或者不介意卸载 Azure Az 模块，最佳做法是使用 `Install-Script` 选项运行该脚本。
* 如果需要保留 Azure Az 模块，则最佳做法是下载并直接运行该脚本。

若要确定是否安装了 Azure Az 模块，请运行 `Get-InstalledModule -Name az`。 如果未看到任何已安装的 Az 模块，可以使用 `Install-Script` 方法。

### <a name="install-using-the-install-script-method"></a>使用 Install-Script 方法安装

只有尚未在计算机上安装 Azure Az 模块时才能使用此选项。 如果已安装，则以下命令将显示错误。 可以卸载 Azure Az 模块，或者另一个选项手动下载并运行该脚本。
  
通过以下命令运行该脚本以获取最新版本：

`Install-Script -Name AzureAppGWMigration -Force`

此命令还会安装所需的 Az 模块。  

### <a name="install-using-the-script-directly"></a>直接使用脚本安装

如果已安装某些 Azure Az 模块并且无法卸载它们（或者不想卸载），可以使用脚本下载链接中的“手动下载”选项卡手动下载该脚本。  此脚本将作为原始 nupkg 文件下载。 若要安装此 nupkg 文件中的脚本，请参阅[手动下载包](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)。

若要运行该脚本，请执行以下操作：

1. 使用 `Connect-AzAccount` 连接到 Azure。

1. 使用 `Import-Module Az` 导入 Az 模块。

1. 运行 `Get-Help AzureAppGWMigration.ps1` 检查所需的参数：

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceId <public IP name string>
    -validateMigration -enableAutoScale
   ```

   脚本的参数：
   * **resourceId: [String]:Required** - 这是现有的 Standard v1 或 WAF v1 网关的 Azure 资源 ID。 若要查找此字符串值，请导航到 Azure 门户，选择你的应用程序网关或 WAF 资源，然后单击网关对应的“属性”链接。  资源 ID 位于该页上。

     也可以运行以下 Azure PowerShell 命令获取资源 ID：

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [String]:Required** - 这是为包含新 v2 网关的新子网分配（或想要分配）的 IP 地址空间。 必须以 CIDR 表示法指定此参数。 例如：10.0.0.0/24。 无需提前创建此子网。 如果此子网不存在，脚本将会创建它。
   * **appgwName: [String]:Optional**。 这是指定用作新 Standard_v2 或 WAF_v2 网关的名称的字符串。 如果未提供此参数，则会使用现有 v1 网关的名称并在其后追加后缀 *_v2*。
   * **sslCertificates: [PSApplicationGatewaySslCertificate]:Optional**。  创建的 PSApplicationGatewaySslCertificate 对象的逗号分隔列表，这些对象表示 v1 网关中必须上传到新 v2 网关的 TLS/SSL 证书。 对于为 Standard v1 或 WAF v1 网关配置的每个 TLS/SSL 证书，可按如下所示通过 `New-AzApplicationGatewaySslCertificate` 命令创建新的 PSApplicationGatewaySslCertificate 对象。 需要 TLS/SSL 证书文件的路径和密码。

     仅当没有为 v1 网关或 WAF 配置 HTTPS 侦听器时，此参数才是可选项。 如果至少安装了一个 HTTPS 侦听器，则必须指定此参数。

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     在以上示例中，可以传入 `$mySslCert1, $mySslCert2`（逗号分隔）作为脚本中此参数的值。
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]:Optional**。 创建的 PSApplicationGatewayTrustedRootCertificate 对象的逗号分隔列表，表示用于对 v2 网关中后端实例进行身份验证的[受信任根证书](ssl-overview.md)。
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      若要创建 PSApplicationGatewayTrustedRootCertificate 对象列表，请参阅 [AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0)。
   * **privateIpAddress: [String]:Optional**。 要关联到新 v2 网关的特定专用 IP 地址。  此地址必须来自为新 v2 网关分配的同一 VNet。 如果未指定，该脚本将为 v2 网关分配一个专用 IP 地址。
   * **publicIpResourceId: [String]:Optional**。 订阅中要分配给新 v2 网关的现有公共 IP 地址（标准 SKU）资源的 resourceId。 如果未指定参数，该脚本将在同一资源组中分配一个新的公共 IP。 名称是追加了“-IP”  的 v2 网关名称。
   * **validateMigration: [switch]:Optional**。 如果你希望在创建 v2 网关并复制配置后让脚本执行一些基本的配置比较验证，请使用此参数。 默认不会执行任何验证。
   * **enableAutoScale: [switch]:Optional**。 如果你希望在创建新的 v2 网关后让脚本启用自动缩放，请使用此参数。 默认会禁用自动缩放。 以后，始终可以在创建新的 v2 网关后手动启用自动缩放。

1. 使用相应的参数运行脚本。 完成该脚本可能需要 5 到 7 分钟时间。

    **示例**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $mySslCert1,$mySslCert2 `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "/subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/publicIPAddresses/MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>迁移客户端流量

首先，请仔细检查脚本是否已成功创建了一个新的 v2 网关，其中包含要从 v1 网关迁移的确切配置。 可以从 Azure 门户验证此结果。

另外，请通过 v2 网关发送少量的流量作为手动测试。
  
在以下几种情况下，当前的应用程序网关 (Standard) 可以接收客户端流量，我们针对每种情况提供了建议：

* **自定义 DNS 区域（例如 contoso.com）指向与 Standard v1 或 WAF v1 网关关联的前端 IP 地址（使用 A 记录）** 。

    可以更新 DNS 记录，使其指向与 Standard_v2 应用程序网关关联的前端 IP 或 DNS 标签。 根据 DNS 记录中配置的 TTL，可能需要一段时间才能将所有客户端流量迁移到新的 v2 网关。
* **一个自定义 dns 区域（例如 contoso.com），指向与 v1 网关关联的 dns 标签（例如： *myappgw.eastus.cloudapp.azure.com* ，使用 CNAME 记录）**。

   有两种选择：

  * 如果在应用程序网关上使用公共 IP 地址，则可以使用流量管理器配置文件执行受控的粒度迁移，以增量方式将流量路由到新的 v2 网关（加权流量路由方法）。

    为此，可以将 v1 和 v2 应用程序网关的 DNS 标签添加到[流量管理器配置文件](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)，并将自定义 DNS 记录（例如， `www.contoso.com` ） CNAMEing 到流量管理器域（例如，contoso.trafficmanager.net）。
  * 或者，可以更新自定义域的 DNS 记录，使其指向新 v2 应用程序网关的 DNS 标签。 根据 DNS 记录中配置的 TTL，可能需要一段时间才能将所有客户端流量迁移到新的 v2 网关。
* **客户端连接到应用程序网关的前端 IP 地址**。

   更新客户端，以使用与新建的 v2 应用程序网关关联的 IP 地址。 我们建议不要直接使用 IP 地址。 请考虑使用与你的应用程序网关关联的 DNS 名称标签（例如，yourgateway.eastus.cloudapp.azure.com），可以将其 CNAME 到你自己的自定义 DNS 区域（例如，contoso.com）。

## <a name="common-questions"></a>常见问题

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>用于将配置从 v1 迁移到 v2 的 Azure PowerShell 脚本是否存在任何限制？

是的。 请参阅[注意事项/限制](#caveatslimitations)。

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>本文和上述 Azure PowerShell 脚本是否也适用于应用程序网关 WAF 产品？ 

是的。

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>该 Azure PowerShell 脚本是否还可以将流量从 v1 网关切换到新建的 v2 网关？

否。 该 Azure PowerShell 脚本只会迁移配置。 实际的流量迁移由你负责和控制。

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>该 Azure PowerShell 脚本创建的新 v2 网关是否大小适当，可以处理当前由 v1 网关提供服务的所有流量？

该 Azure PowerShell 脚本将创建适当大小的新 v2 网关来处理现有 v1 网关上的流量。 默认会禁用自动缩放，但你可以在运行脚本时启用自动缩放。

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>我已将 v1 网关配置为向 Azure 存储发送日志。 该脚本是否也会为 v2 复制此配置？

否。 该脚本不会为 v2 复制此配置。 必须单独将日志配置添加到迁移后的 v2 网关。

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>此脚本是否支持上传到 Azure KeyVault 的证书？

否。 该脚本目前不支持 KeyVault 中的证书。 但是，我们正在考虑在将来的版本中添加此功能。

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>使用此脚本时我遇到了一些问题。 如何求助？
  
你可以在主题 "配置和设置/迁移到 V2 SKU" 下联系 Azure 支持。 [在此处了解有关 Azure 支持](https://azure.microsoft.com/support/options/)的详细信息。

## <a name="next-steps"></a>后续步骤

[了解应用程序网关 v2](application-gateway-autoscaling-zone-redundant.md)
