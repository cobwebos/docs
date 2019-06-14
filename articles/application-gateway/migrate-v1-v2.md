---
title: 迁移 Azure 应用程序网关和 Web 应用程序防火墙从 v1 到 v2
description: 本文介绍如何从 v1 到 v2 迁移 Azure 应用程序网关和 Web 应用程序防火墙
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: 2387f2546afa9d5af2cb909a1e6a2179548e3b5a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053328"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>迁移 Azure 应用程序网关和 Web 应用程序防火墙从 v1 到 v2

[Azure 应用程序网关和 Web 应用程序防火墙 (WAF) v2](application-gateway-autoscaling-zone-redundant.md)现已推出，提供其他功能，例如自动缩放和可用性区域冗余。 但是，现有 v1 网关不会自动升级到 v2。 如果你想要将迁移从 v1 到 v2，请按照本文中的步骤。

在迁移中有两个阶段：

1. 迁移的配置
2. 迁移客户端流量

本文介绍如何配置迁移。 客户端流量迁移各不相同，具体取决于你的特定环境。 但是，一些高级的常规建议[提供了](#migrate-client-traffic)。

## <a name="migration-overview"></a>迁移概述

提供了 Azure PowerShell 脚本执行以下操作：

* 您指定的虚拟网络子网中创建新的 Standard_v2 或 WAF_v2 网关。
* 无缝地将复制到新创建的 Standard_V2 或 WAF_V2 网关 v1 标准或 WAF 网关与关联的配置。

### <a name="caveatslimitations"></a>Caveats\Limitations

* 新 v2 网关具有新的公共和专用 IP 地址。 它不能移动与现有 v1 到 v2 的无缝的网关相关联的 IP 地址。 但是，你可以分配现有 （未分配） 公用或专用 IP 地址到新 v2 网关。
* V1 网关所在的位置在虚拟网络中，必须为另一个子网提供 IP 地址空间。 该脚本不能在任何已有 v1 网关的现有子网中创建 v2 网关。 但是，如果已有现有的子网的第 2 版网关，仍可能正常工作提供足够的 IP 地址空间。
* 若要迁移的 SSL 配置，必须指定使用 v1 网关中的所有 SSL 证书。
* 如果必须为 V1 网关启用了 FIPS 模式，它不会迁移到新的 v2 网关。 V2 不支持 FIPS 模式。
* v2 不支持 IPv6，因此在启用了 IPv6 v1 网关不会迁移。 如果运行该脚本，可能无法完成。
* 如果 v1 网关仅专用 IP 地址，该脚本将创建一个公共 IP 地址和专用 IP 地址的新 v2 网关。 第 2 版网关当前不支持仅使用专用 IP 地址。

## <a name="download-the-script"></a>下载脚本

下载中的迁移脚本[PowerShell 库](https://www.powershellgallery.com/packages/AzureAppGWMigration)。

## <a name="use-the-script"></a>使用脚本

有两个选项，具体取决于你的本地 PowerShell 环境设置和首选项：

* 如果您没有安装，Azure Az 模块或不介意卸载 Azure Az 模块，最佳选择是使用`Install-Script`选项来运行脚本。
* 如果需要保留 Azure Az 模块，最好的方法就是下载该脚本并直接运行。

若要确定你是否正在安装的 Azure Az 模块，请运行`Get-InstalledModule -Name az`。 如果看不到任何已安装 Az 模块，则可以使用`Install-Script`方法。

### <a name="install-using-the-install-script-method"></a>使用安装脚本方法进行安装

若要使用此选项，不能在计算机上安装的 Azure Az 模块。 如果安装这些功能，下面的命令将显示错误。 可以卸载 Azure Az 模块，或使用另一个选项来手动下载该脚本并运行它。
  
使用以下命令运行该脚本：

`Install-Script -Name AzureAppGWMigration`

此命令还会安装所需的 Az 模块。  

### <a name="install-using-the-script-directly"></a>直接使用该脚本进行安装

如果有安装某些 Azure Az 模块并在不能卸载它们 （或不想要卸载它们），则可以手动下载脚本使用**手动下载**脚本下载链接中的选项卡。 该脚本被下载为原始 nupkg 文件。 若要从此 nupkg 文件安装脚本，请参阅[手动包下载](https://docs.microsoft.com/powershell/gallery/how-to/working-with-packages/manual-download)。

若要运行该脚本，请执行以下操作：

1. 使用`Connect-AzAccount`连接到 Azure。

1. 使用`Import-Module Az`Az 模块导入。

1. 运行`Get-Help AzureAppGWMigration.ps1`检查所需的参数：

   `AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceName <public IP name string>
    -validateMigration -enableAutoScale`

   脚本的参数：
   * **资源 Id: [字符串]:所需**-这是你的现有标准 v1 或 WAF v1 网关的 Azure 资源 ID。 若要查找此字符串值，导航到 Azure 门户中，选择你的应用程序网关或 WAF 的资源，并单击**属性**网关的链接。 资源 ID 位于该页面上。

     此外可以运行以下 Azure PowerShell 命令获取资源 ID:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [字符串]:所需**-这是包含新 v2 网关的新子网的 IP 地址空间，已分配 （或想要分配）。 这必须在 CIDR 表示法中指定。 例如：10.0.0.0/24. 不需要提前创建此子网。 该脚本创建它，如果不存在。
   * **appgwName: [字符串]:可选**。 这是一个字符串，指定要用作新 Standard_v2 或 WAF_v2 网关的名称。 如果不提供此参数，现有的 v1 网关的名称将使用带有后缀 *_v2*追加。
   * **sslCertificates: [PSApplicationGatewaySslCertificate]:可选**。  必须将以逗号分隔列表的 PSApplicationGatewaySslCertificate 对象的创建用于表示从 v1 网关的 SSL 证书上载到新 v2 网关。 对于每个配置为使用标准 v1 或 WAF v1 网关将 SSL 证书，可以创建一个新的 PSApplicationGatewaySslCertificate 对象通过`New-AzApplicationGatewaySslCertificate`命令如下所示。 为你的 SSL 证书文件并将密码需要的路径。

       此参数才是可选的如果没有为 v1 网关或 WAF 配置的 HTTPS 侦听程序。 如果必须至少一个 HTTPS 侦听程序安装程序，则必须指定此参数。

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

      可以传入`$mySslCert1, $mySslCert2`（以逗号分隔） 在前面的示例作为此参数在脚本中的值。
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]:可选**。 创建用于表示 PSApplicationGatewayTrustedRootCertificate 对象的以逗号分隔列表[受信任的根证书](ssl-overview.md)从 v2 网关后端实例的身份验证。  

      若要创建的 PSApplicationGatewayTrustedRootCertificate 对象的列表，请参阅[新建 AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0)。
   * **privateIpAddress: [字符串]:可选**。 特定专用 IP 地址，你想要关联到新的 v2 网关。  这必须是从同一个 VNet 分配给新 v2 网关。 如果这未指定，脚本会 v2 网关分配专用 IP 地址。
    * **publicIpResourceId: [String]:可选**。 你想要分配给新 v2 网关的订阅中的公共 IP 地址 (标准 SKU) 资源的 resourceId。 如果未指定，脚本将分配同一资源组中的新公共 IP。 名称是 v2 网关的名称与 *-IP*追加。
   * **validateMigration: [开关]:可选**。 如果你想要在第 2 版网关创建并配置复制后执行一些基本的配置比较验证的脚本，请使用此参数。 默认情况下不进行任何验证。
   * **enableAutoScale: [开关]:可选**。 如果你想要在新 v2 网关上启用自动缩放，在创建后的脚本，请使用此参数。 默认情况下，禁用自动缩放。 你可以始终手动启用它更高版本上新创建的第 2 版网关。

1. 运行该脚本使用适当的参数。 可能需要 5 至 7 分钟时间才能完成。

    **示例**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $Certs `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceName "MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>迁移客户端流量

首先，仔细检查该脚本成功创建新的 v2 网关具有完全的配置从 v1 网关迁移过来。 可以从 Azure 门户对此进行验证。

此外，作为手动测试发送少量的流量通过 v2 网关。
  
下面是几种方案，其中在当前应用程序网关 （标准） 可能会收到客户端流量，并为每个我们建议：

* **与标准 v1 或 WAF v1 网关相关联的自定义的 DNS 区域 (例如，contoso.com) 到 （使用 A 记录） 的前端 IP 地址指向**。

    可以更新你的 DNS 记录以指向 Standard_v2 应用程序网关相关联的前端 IP 或 DNS 标签。 具体取决于你的 DNS 记录上配置的 TTL，它可能需要一些时间对您的所有客户端流量，以迁移到新的 v2 网关。
* **自定义的 DNS 区域 (例如，contoso.com) 指向 DNS 标签 (例如： *myappgw.eastus.cloudapp.azure.com*使用 CNAME 记录) 与 v1 网关关联**。

   有两种选择：

  * 如果您的应用程序网关上使用公共 IP 地址，则可以执行受控精细迁移使用流量管理器配置文件以增量方式将流量 （加权的流量路由方法） 路由到新的 v2 网关。

    您可以执行此操作通过添加到 v1 和 v2 应用程序网关的 DNS 标签[流量管理器配置文件](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)，并因此 Traffic Manager 域 （例如，在自定义 DNS 记录 (例如，www.contoso.com)contoso.trafficmanager.net)。
  * 或者，可以更新你的自定义域的 DNS 记录以指向新的 v2 应用程序网关的 DNS 标签。 具体取决于你的 DNS 记录上配置的 TTL，它可能需要一些时间对您的所有客户端流量，以迁移到新的 v2 网关。
* **你的客户端连接到前端的应用程序网关的 IP 地址**。

   更新客户端可以使用与新创建的 v2 应用程序网关相关联的 IP 地址。 我们建议不要直接使用 IP 地址。 请考虑使用 DNS 名称标签 (例如，yourgateway.eastus.cloudapp.azure.com) 与应用程序网关，你可以给你自己自定义 DNS 区域 (例如，contoso.com) 的 CNAME。

## <a name="common-questions"></a>常见问题

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>是否有要迁移的配置从 v1 到 v2 的 Azure PowerShell 脚本的任何限制？

是的。 请参阅[需要注意的问题/限制](#caveatslimitations)。

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>这篇文章和 Azure PowerShell 脚本是适用于应用程序网关 WAF 产品和的？ 

是的。

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Azure PowerShell 脚本不会还切换到新创建的第 2 版网关我 v1 的网关的流量通过？

不。 Azure PowerShell 脚本只会迁移配置。 实际流量迁移是您有责任在您的控件。

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>是通过 Azure PowerShell 脚本创建的新 v2 网关大小进行适当调整以处理所有当前由我 v1 的网关的流量？

Azure PowerShell 脚本使用适当的大小来处理您现有的 v1 网关上的流量创建新的 v2 网关。 默认情况下，禁用自动缩放，但在运行该脚本时，可以启用自动缩放。

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>我配置了我 v1 的网关将日志发送到 Azure 存储。 该脚本是否复制 v2 还为此配置？

不。 该脚本不会复制 v2 的此配置。 到已迁移的第 2 版网关必须单独添加日志配置。

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>我遇到了一些使用此脚本的问题。 如何获得帮助？
  
可以发送电子邮件至appgwmigrationsup@microsoft.com、 打开支持案例与 Azure 支持，或同时利用这两者。

## <a name="next-steps"></a>后续步骤

[了解有关应用程序网关 v2](application-gateway-autoscaling-zone-redundant.md)
