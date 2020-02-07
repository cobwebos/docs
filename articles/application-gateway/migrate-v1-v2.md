---
title: 从 v1 迁移到 v2-Azure 应用程序的网关
description: 本文介绍如何将 Azure 应用程序网关和 Web 应用程序防火墙从 v1 迁移到 v2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 9909c46015fffb3bea3eef094599312e28b935c5
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046200"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>将 Azure 应用程序网关和 Web 应用程序防火墙从 v1 迁移到 v2

[Azure 应用程序网关和 Web 应用程序防火墙（WAF） v2](application-gateway-autoscaling-zone-redundant.md)现在可用，提供其他功能，例如自动缩放和可用性-区域冗余。 但是，现有 v1 网关不会自动升级到 v2。 如果要从 v1 迁移到 v2，请按照本文中的步骤进行操作。

迁移分为两个阶段：

1. 迁移配置
2. 迁移客户端流量

本文介绍了配置迁移。 客户端流量迁移因特定环境而异。 不过，[还提供了](#migrate-client-traffic)一些高级建议。

## <a name="migration-overview"></a>迁移概述

可执行以下操作的 Azure PowerShell 脚本：

* 在指定的虚拟网络子网中创建新的 Standard_v2 或 WAF_v2 的网关。
* 将与 v1 标准或 WAF 网关关联的配置无缝复制到新创建的 Standard_V2 或 WAF_V2 网关。

### <a name="caveatslimitations"></a>Caveats\Limitations

* 新的 v2 网关具有新的公共和专用 IP 地址。 不能将与现有 v1 网关关联的 IP 地址无缝地移动到 v2。 但是，可以将现有的（未分配的）公用或专用 IP 地址分配给新的 v2 网关。
* 你必须为你的 v1 网关所在的虚拟网络中的另一个子网提供 IP 地址空间。 此脚本无法在已有 v1 网关的任何现有子网中创建 v2 网关。 但是，如果现有子网已具有 v2 网关，则在提供足够的 IP 地址空间时，该网关可能仍会运行。
* 若要迁移 SSL 配置，必须指定 v1 网关上使用的所有 SSL 证书。
* 如果已为 V1 网关启用 FIPS 模式，则不会将其迁移到新的 v2 网关。 V2 不支持 FIPS 模式。
* v2 不支持 IPv6，因此启用了 IPv6 的 v1 网关不会迁移。 如果运行该脚本，则它可能无法完成。
* 如果 v1 网关只有专用 IP 地址，则该脚本将创建一个公共 IP 地址和一个专用 IP 地址，以用于新的 v2 网关。 v2 网关当前不支持专用 IP 地址。

## <a name="download-the-script"></a>下载脚本

从[PowerShell 库](https://www.powershellgallery.com/packages/AzureAppGWMigration)下载迁移脚本。

## <a name="use-the-script"></a>使用脚本

根据本地 PowerShell 环境的设置和首选项，有两个选项可供选择：

* 如果尚未安装 Azure Az 模块，或不介意卸载 Azure Az 模块，最佳选择是使用 `Install-Script` 选项运行脚本。
* 如果需要保留 Azure Az 模块，最佳做法是下载并直接运行该脚本。

若要确定是否安装了 Azure Az 模块，请运行 `Get-InstalledModule -Name az`。 如果看不到任何已安装的 Az 模块，则可以使用 `Install-Script` 方法。

### <a name="install-using-the-install-script-method"></a>使用安装脚本方法安装

若要使用此选项，你必须在计算机上未安装 Azure Az 模块。 如果已安装，则以下命令将显示错误。 可以卸载 Azure Az 模块，也可以使用其他选项手动下载并运行该脚本。
  
使用以下命令运行该脚本：

`Install-Script -Name AzureAppGWMigration`

此命令还会安装所需的 Az 模块。  

### <a name="install-using-the-script-directly"></a>直接使用脚本安装

如果你安装了某些 Azure Az 模块并且无法卸载它们（或不想卸载它们），可以使用脚本下载链接中的 "**手动下载**" 选项卡手动下载该脚本。 此脚本将作为原始 nupkg 文件下载。 若要安装此 nupkg 文件中的脚本，请参阅[手动包下载](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)。

若要运行该脚本，请执行以下操作：

1. 使用 `Connect-AzAccount` 连接到 Azure。

1. 使用 `Import-Module Az` 导入 Az 模块。

1. 运行 `Get-Help AzureAppGWMigration.ps1` 以检查所需的参数：

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
   * **resourceId： [String]：必需**-这是适用于现有标准 V1 或 WAF v1 网关的 AZURE 资源 ID。 若要查找此字符串值，请导航到 Azure 门户，选择应用程序网关或 WAF 资源，并单击网关的 "**属性**" 链接。 资源 ID 位于该页上。

     你还可以运行以下 Azure PowerShell 命令来获取资源 ID：

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange： [String]：必需**-这是为包含新 v2 网关的新子网分配（或要分配）的 IP 地址空间。 这必须以 CIDR 表示法指定。 例如： 10.0.0.0/24。 无需提前创建此子网。 如果不存在，脚本将为你创建它。
   * **appgwName： [String]：可选**。 这是你指定用作新 Standard_v2 或 WAF_v2 网关的名称的字符串。 如果未提供此参数，则会将现有 v1 网关的名称与追加 *_v2*后缀一起使用。
   * **azure： [PSApplicationGatewaySslCertificate]：可选**。  你创建的 PSApplicationGatewaySslCertificate 对象列表以逗号分隔，你创建的这些对象用于表示你的 v1 网关中的 SSL 证书。 对于为标准 v1 或 WAF v1 网关配置的每个 SSL 证书，可以通过此处显示的 `New-AzApplicationGatewaySslCertificate` 命令创建新的 PSApplicationGatewaySslCertificate 对象。 你需要 SSL 证书文件的路径和密码。

     仅当没有为 v1 网关或 WAF 配置 HTTPS 侦听器时，此参数才是可选的。 如果至少安装了一个 HTTPS 侦听器，则必须指定此参数。

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     您可以在上一示例中以逗号分隔的形式传递 `$mySslCert1, $mySslCert2` 脚本中此参数的值。
   * **trustedRootCertificates： [PSApplicationGatewayTrustedRootCertificate]：可选**。 一个逗号分隔列表，其中列出了你创建的 PSApplicationGatewayTrustedRootCertificate 对象，用于表示从 v2 网关对后端实例进行身份验证的[受信任根证书](ssl-overview.md)。
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      若要创建 PSApplicationGatewayTrustedRootCertificate 对象列表，请参阅[AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0)。
   * **privateIpAddress： [String]：可选**。 要关联到新 v2 网关的特定专用 IP 地址。  此配置必须来自为新 v2 网关分配的同一 VNet。 如果未指定，则脚本将为 v2 网关分配专用 IP 地址。
   * **publicIpResourceId： [String]：可选**。 你的订阅中要分配给新 v2 网关的现有公共 IP 地址（标准 SKU）资源的 resourceId。 如果未指定此项，则脚本将在同一资源组中分配一个新的公共 IP。 名称是附加了 *-IP*的 v2 网关的名称。
   * **validateMigration： [开关]：可选**。 如果希望脚本在创建 v2 网关和配置副本后执行一些基本配置比较验证，请使用此参数。 默认情况下，不执行任何验证。
   * **enableAutoScale： [开关]：可选**。 如果希望脚本在创建新 v2 网关后启用自动缩放，请使用此参数。 默认情况下，自动缩放处于禁用状态。 稍后，你始终可以在新创建的 v2 网关上手动启用它。

1. 使用适当的参数运行该脚本。 完成此操作可能需要5到7分钟。

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

首先，请仔细检查脚本是否已成功创建了一个新的 v2 网关，并从 v1 网关上迁移了完全相同的配置。 可以从 Azure 门户验证此验证。

此外，通过 v2 网关发送少量流量作为手动测试。
  
在以下几种情况下，当前应用程序网关（标准）可能会接收客户端流量，并为每个应用程序网关提供建议：

* **一个自定义 DNS 区域（例如，contoso.com），指向与标准 v1 或 WAF v1 网关关联的前端 IP 地址（使用 A 记录）** 。

    可以更新 DNS 记录，使其指向与 Standard_v2 应用程序网关关联的前端 IP 或 DNS 标签。 根据 DNS 记录上配置的 TTL，可能需要一段时间才能将所有客户端流量迁移到新的 v2 网关。
* **一个自定义 dns 区域（例如 contoso.com），指向与 v1 网关关联的 dns 标签（例如： *myappgw.eastus.cloudapp.azure.com* ，使用 CNAME 记录）** 。

   你有两个选项：

  * 如果你在应用程序网关上使用公共 IP 地址，则可以使用流量管理器配置文件执行受控的粒度迁移，以将流量（加权流量路由方法）增量路由到新的 v2 网关。

    为此，可以将 v1 和 v2 应用程序网关的 DNS 标签添加到[流量管理器配置文件](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)，并将自定义 DNS 记录（例如 `www.contoso.com`） CNAMEing 到流量管理器域（例如 contoso.trafficmanager.net）。
  * 或者，你可以将自定义域 DNS 记录更新为指向新的 v2 应用程序网关的 DNS 标签。 根据 DNS 记录上配置的 TTL，可能需要一段时间才能将所有客户端流量迁移到新的 v2 网关。
* **你的客户端连接到你的应用程序网关的前端 IP 地址**。

   更新客户端以使用与新创建的 v2 应用程序网关关联的 IP 地址。 建议你不要直接使用 IP 地址。 请考虑使用与你的应用程序网关关联的 DNS 名称标签（例如，yourgateway.eastus.cloudapp.azure.com），可以将其 CNAME 到你自己的自定义 DNS 区域（例如，contoso.com）。

## <a name="common-questions"></a>常见问题

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Azure PowerShell 脚本是否存在将配置从 v1 迁移到 v2 的任何限制？

可以。 请参阅[注意事项/限制](#caveatslimitations)。

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>这篇文章和 Azure PowerShell 脚本是否适用于应用程序网关 WAF 产品？ 

可以。

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Azure PowerShell 脚本还会将通信从我的 v1 网关切换到新创建的 v2 网关吗？

No。 Azure PowerShell 脚本仅迁移配置。 实际的流量迁移是您在控制中的责任。

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Azure PowerShell 脚本所创建的新 v2 网关是否经过适当调整以处理当前由 v1 网关提供的所有通信？

Azure PowerShell 脚本使用适当的大小创建一个新的 v2 网关，以处理现有 v1 网关上的流量。 默认情况下，自动缩放处于禁用状态，但你可以在运行脚本时启用自动缩放。

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>我配置了 v1 网关，以将日志发送到 Azure 存储。 脚本是否也将此配置复制到 v2？

No。 此脚本不会复制 v2 的此配置。 必须将日志配置单独添加到迁移的 v2 网关。

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>此脚本是否支持上传到 Azure KeyVault 的证书？

No。 此脚本当前不支持 KeyVault 中的证书。 但是，在将来的版本中会考虑这一点。

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>使用此脚本时，我遇到了一些问题。 如何获取帮助？
  
你可以向 appgwmigrationsup@microsoft.com发送电子邮件、使用 Azure 支持提供支持案例，或同时执行这两项操作。

## <a name="next-steps"></a>后续步骤

[了解应用程序网关 v2](application-gateway-autoscaling-zone-redundant.md)
