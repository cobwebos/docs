---
title: 使用资源管理器模板创建 Azure 应用服务环境
description: 阐释如何使用资源管理器模板创建外部或 ILB Azure 应用服务环境
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: d85384620b2e4c7ba0de84e0fe82ef3e83376dd8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板创建 ASE

## <a name="overview"></a>概述
可通过能访问 Internet 的终结点或 Azure 虚拟网络 (VNet) 内部地址上的终结点创建 Azure 应用服务环境 (ASE)。 使用内部终结点创建时，该终结点由被称为内部负载均衡器 (ILB) 的 Azure 组件提供。 内部 IP 地址上的 ASE 被称为 ILB ASE。 具有公共终结点的 ASE 被称为外部 ASE。 

可使用 Azure 门户或 Azure 资源管理器模板创建 ASE。 本文介绍使用资源管理器模板创建外部 ESE 或 ILB ASE 所需的步骤和语法。 要了解如何在 Azure 门户中创建 ASE，请参阅[创建外部 ASE][MakeExternalASE] 或[创建 ILB ASE][MakeILBASE]。

在 Azure 门户中创建 ASE 时，可同时创建自己的 VNet 或选择要部署到的预先存在的 VNet。 基于模板创建 ASE 时，必须首先具有： 

* 一个资源管理器 VNet。
* 该 VNet 中的子网。 建议使用带 128 位地址的大小为 `/25` 的 ASE 子网，以适应将来的增长需求。 创建 ASE 后，就无法更改其大小。
* 来自 VNet 的资源 ID。 此信息位于 Azure 门户下的虚拟网络属性中。
* 要部署到的订阅。
* 要部署到的位置。

若要自动创建 ASE：

1. 基于模板创建 ASE。 若要创建外部 ASE，则完成此步骤后停止。 若要创建 ILB ASE，还需执行其他几项操作。

2. ILB ASE 创建完毕后，将上传一个匹配 ILB ASE 域的 SSL 证书。

3. 上传的 SSL 证书将分配到 ILB ASE 作为其“默认”SSL 证书。  当应用使用分配给 ASE 的一般根域（如 https://someapp.mycustomrootdomain.com)）时，此证书用于 ILB ASE 上流向应用的 SSL 流量。


## <a name="create-the-ase"></a>创建 ASE
有关用于创建 ASE 的资源管理器模板及其关联的参数文件，可参阅 GitHub 上的[示例][quickstartasev2create]。

要创建 ILB ASE，请使用这些资源管理器模板[示例][quickstartilbasecreate]。 它们适用于该用例。 azuredeploy.parameters.json 文件中的大部分参数常用于创建 ILB ASE 和外部 ASE。 创建 ILB ASE 时，以下列表会调出特殊注释的参数或唯一的参数：

* *internalLoadBalancingMode*：此属性多数情况下设置为 3，这表示端口 80/443 上的 HTTP/HTTPS 流量以及 ASE 上的 FTP 服务所侦听的控制/数据通道端口将绑定到 ILB 分配的虚拟网络内部地址。 如果此属性设置为 2，则仅将与 FTP 服务相关的端口（包括控制和数据信道）绑定至 ILB 地址。 HTTP/HTTPS 流量保留在公共 VIP 中。
* dnsSuffix：此参数定义要分配给 ASE 的默认根域。 在 Azure 应用服务的公共变体中，所有 Web 应用的默认根域均为 *azurewebsites.net*。 由于 ILB ASE 位于客户虚拟网络的内部，因此不适合使用公共服务的默认根域。 而应当具有适合在公司的内部虚拟网络中使用的默认根域。 例如，Contoso Corporation 可能会将 internal-contoso.com 的默认根域用于只能在 Contoso 虚拟网络内解析和访问的应用。 
* ipSslAddressCount：在 azuredeploy.json 文件中，此参数的值自动默认为 0，因为 ILB ASE 只有一个 ILB 地址。 ILB ASE 没有显式 IP-SSL 地址。 因此，ILB ASE 的 IP-SSL 地址池应设置为零。 否则将出现预配错误。 

在填充 azuredeploy.parameters.json 文件后，使用 PowerShell 代码片段来创建 ASE。 更改文件路径，以匹配资源管理器模板文件在计算机上的位置。 切记提供自己的资源管理器部署名称值和资源组名称值：

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

创建 ASE 可能需要约一小时。 然后，对于触发部署的订阅，ASE 将显示在门户的 ASE 列表中。

## <a name="upload-and-configure-the-default-ssl-certificate"></a>上传和配置“默认”SSL 证书
SSL 证书必须与 ASE 关联，作为用于建立应用的 SSL 连接的“默认”SSL 证书。 如果 ASE 的默认 DNS 后缀是 internal-contoso.com，则需要对 *.internal-contoso.com 有效的 SSL 证书才可连接到 https://some-random-app.internal-contoso.com。 

可通过三种方式获取有效的 SSL 证书：使用内部证书颁发机构、向外部颁发者购买证书或使用自签名证书。 无论 SSL 证书的来源如何，都需要正确配置以下证书属性：

* 使用者：此属性必须设置为 *.your-root-domain-here.com。
* 使用者可选名称：此属性必须同时包含 *.your-root-domain-here.com 和 *scm.your-root-domain-here.com。使用 your-app-name.scm.your-root-domain-here.com 形式的地址，建立与每个应用关联的 SCM/Kudu 站点的 SSL 连接。

备妥有效的 SSL 证书还需要两个额外的准备步骤。 将 SSL 证书转换/保存为 .pfx 文件。 请记住，.pfx 文件必须包括所有的中间和根证书。 使用密码进行保护。

需要将 .pfx 文件转换为 base64 字符串，因为将使用资源管理器模板上传 SSL 证书。 由于资源管理器模板是文本文件，因此必须将 .pfx 文件转换为 base64 字符串。 如此才可将其作为模板的参数包含在内。

通过下列 PowerShell 代码片段：

* 生成自签名证书。
* 将证书导出为.pfx 文件。
* 将 .pfx 文件转换为 base64 编码的字符串。
* 将 base64 编码的字符串保存到单独的文件。 

base64 编码的 Powershell 代码改写自 [PowerShell 脚本博客][examplebase64encoding]：

```powershell
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

成功生成 SSL 证书并转换为 base64 编码字符串后，使用 GitHub 上的示例资源管理器模板[配置默认 SSL 证书][quickstartconfiguressl]。 

azuredeploy.parameters.json 文件中的参数如下所列：

* appServiceEnvironmentName：要配置的 ILB ASE 的名称。
* existingAseLocation：包含 ILB ASE 部署所在的 Azure 区域的文本字符串。  例如“美国中南部”。
* pfxBlobString：.pfx 文件的 based64 编码字符串表示形式。 使用先前所示的代码片段并复制“exportedcert.pfx.b64”中包含的字符串。 将其作为 pfxBlobString 属性的值进行粘贴。
* password：用于保护 .pfx 文件的密码。
* certificateThumbprint：证书的指纹。 如果从 Powershell 中检索到此值（例如先前代码片段中的 $certificate.Thumbprint），可按原样使用此值。 如果从 Windows 证书对话框复制此值，请记得去除多余的空格。 certificateThumbprint 应如下所示：AF3143EB61D43F6727842115BB7F17BBCECAECAE。
* certificateName：用户自己选择的易记字符串标识符，用于标识证书。 此名称用作 Microsoft.Web/certificates 实体（表示 SSL 证书）的资源管理器唯一标识符的一部分。 名称必须以下述后缀结尾：\_yourASENameHere_InternalLoadBalancingASE。 此后缀由 Azure 门户使用，表示证书用于维护启用 ILB 的 ASE 的安全。

azuredeploy.parameters.json 的缩写示例如下所示：

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "appServiceEnvironmentName": {
      "value": "yourASENameHere"
    },
    "existingAseLocation": {
      "value": "East US 2"
    },
    "pfxBlobString": {
      "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
    },
    "password": {
      "value": "PASSWORDGOESHERE"
    },
    "certificateThumbprint": {
      "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
    },
    "certificateName": {
      "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
    }
  }
}
```

在填充 azuredeploy.parameters.json 文件后，使用 PowerShell 代码片段来配置默认 SSL 证书。 更改文件路径，以匹配资源管理器模板文件在计算机上的位置。 切记提供自己的资源管理器部署名称值和资源组名称值：

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

每个 ASE 前端约耗时 40 分钟才能应用此更改。 例如，有一个默认大小的 ASE 使用两个前端，则模板需要大约 1 小时 20 分钟才能完成。 运行模板时无法缩放 ASE。  

模板运行完成后，即可通过 HTTPS 访问 ILB ASE 上的应用。 使用默认 SSL 证书来保护连接安全。 如果 ILB ASE 上的应用使用应用程序名称与默认主机名的组合来寻址，则会使用默认 SSL 证书。 例如，https://mycustomapp.internal-contoso.com 使用 *.internal-contoso.com 的默认 SSL 证书。

但是，就像公共多租户服务上运行的应用一样，开发者可为单个应用配置自定义主机名。 还可为单个应用配置唯一的 SNI SSL 证书绑定。

## <a name="app-service-environment-v1"></a>应用服务环境 v1 ##
应用服务环境有两个版本：ASEv1 和 ASEv2。 上述信息基于 ASEv2。 本部分说明 ASEv1 和 ASEv2 之间的差异。

在 ASEv1 中，需手动管理所有资源。 它们包括基于 IP 的 SSL 所用的前端、辅助角色和 IP 地址。 扩大应用服务计划之前，需要先扩大要托管该计划的辅助角色池。

ASEv1 使用与 ASEv2 不同的定价模型。 在 ASEv1 中，需要为分配的每个 vCPU 付费。 包括未托管任何工作负荷的前端或辅助角色所使用的 vCPU。 在 ASEv1 中，ASE 的默认最大规模为 55 个主机总数。 其中包括辅助角色和前端。 ASEv1 的一项优势是可在经典虚拟网络和资源管理器虚拟网络中进行部署。 若要深入了解 ASEv1，请参阅[应用服务环境 v1 简介][ASEv1Intro]。

若要使用资源管理器模板创建 ASEv1，请参阅[使用资源管理器模板创建 ILB ASE v1][ILBASEv1Template]。


<!--Links-->
[quickstartilbasecreate]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
