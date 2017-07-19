---
title: "使用 Resource Manager 模板创建 Azure 应用服务环境"
description: "说明如何使用 Resource Manager 模板创建外部或 ILB Azure 应用服务环境"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 370ab7a32634f04b4776a2a9a84ecaacf11ea617
ms.contentlocale: zh-cn
ms.lasthandoff: 06/26/2017

---
# <a name="how-to-create-an-ase-using-azure-resource-manager-templates"></a>如何使用 Azure Resource Manager 模板创建 ASE

## <a name="overview"></a>概述
可通过能访问 Internet 的终结点或 Azure 虚拟网络 (VNet) 内部地址上的终结点创建应用服务环境 (ASE)。  使用内部终结点创建时，该终结点由被称为内部负载均衡器 (ILB) 的 Azure 组件提供。  拥有公共终结点的 ASE 被称为外部 ASE，而内部 IP 地址上的 ASE 被称为 ILB ASE。  

可使用 Azure 门户或 Azure Resource Manager 模板创建 ASE。  本文介绍使用 Azure Resource Manager 模板创建外部 ESE 或 ILB ASE 所需的步骤和语法。  若要深入了解如何在门户中创建 ASE，可首先[创建外部 ASE][MakeExternalASE] 或[创建 ILB ASE][MakeILBASE]。

在门户中创建 ASE 时，可选择同时创建自己的 VNet 或选择要部署到的预先存在的 VNet。  基于模板创建时，必须首先具有： 
* 一个 Resource Manager VNet
* 该 VNet 中的子网。  建议 ASE 子网掩码为 /25，地址长度 128 位，以适应未来的增长。  ASE 创建后不可更改此项
* 来自 vnet 的资源 ID。  它位于 Azure 门户的 Azure 虚拟网络属性下
* 要部署到的订阅
* 要部署到的位置

若要自动创建 ASE：

1. 基于模板创建 ASE。  如果在创建外部 ASE，在此步骤中已创建完毕。  如果创建 ILB ASE，则还需执行其他几项操作
2. ILB ASE 创建完毕后，将上传一个匹配 ILB ASE 域的 SSL 证书。
3. 上传的 SSL 证书将分配到 ILB ASE 作为其“默认”SSL 证书。  如果应用使用分配给 ASE 的一般根域（例如 https://someapp.mycustomrootcomain.com）来寻址，此 SSL 证书将用于 ILB ASE 上应用的 SSL 流量


## <a name="creating-the-ase"></a>创建 ASE
可参阅 GitHub 上的[此处][quickstartasev2create]，了解创建 ASE 的示例 Azure Resource Manager 模板及其相关联的参数文件。

若要生成 ILB ASE，应转而使用[此处][quickstartilbasecreate]的 Resource Manager 模板。  它们适用于该用例。  azuredeploy.parameters.json 文件中的大部分参数即可用于创建 ILB ASE，又可用于创建外部 ASE。  创建 ILB ASE 时，以下列表会调出特殊注释的参数或唯一的参数：

* *interalLoadBalancingMode*：此属性多数情况下设置为 3，这表示端口 80/443 上的 HTTP/HTTPS 流量以及 ASE 上的 FTP 服务所侦听的控制/数据通道端口将绑定到 ILB 分配的虚拟网络内部地址。  如果此属性改设为 2，则只有与 FTP 服务相关的端口（控制和数据通道）会绑定到 ILB 地址，而 HTTP/HTTPS 流量将保留在公用 VIP 上。
* *dnsSuffix*：此参数定义要分配给 ASE 的默认根域。  在 Azure 应用服务的公共变体中，所有 Web 应用的默认根域均为 *azurewebsites.net*。  不过，ILB ASE 位于客户虚拟网络的内部，因此不适合使用公共服务的默认根域，  而应当具有适合在公司的内部虚拟网络中使用的默认根域。  例如，假定的 Contoso Corporation 可能会将 *internal-contoso.com* 的默认根域用于只能在 Contoso 虚拟网络内解析和访问的应用。 
* *ipSslAddressCount*：在 *azuredeploy.json* 文件中，此参数的值自动默认为 0，因为 ILB ASE 只有一个 ILB 地址。  ILB ASE 没有显式 IP-SSL 地址，因此 ILB ASE 的 IP-SSL 地址池必须设置为零，否则会发生预配错误。 

填入 azuredeploy.parameters.json 文件，即可使用以下 Powershell 代码片段创建 ASE。  更改文件 PATH，以匹配 Azure Resource Manager 模板文件在计算机上的位置。  此外，切记提供自己的 Azure Resource Manager 部署名称值和资源组名称值。

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

提交 Azure Resource Manager 模板后，大约需要 1 小时才能创建完 ASE。  创建完成后，ASE 将在门户 UX 中触发部署的订阅的应用服务环境列表下显示。

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>上传和配置“默认”SSL 证书
创建 ILB ASE 后，SSL 证书应与 ASE 关联，作为用来建立应用的 SSL 连接的“默认”SSL 证书。  继续以假定的 Contoso Corporation 为例，如果 ASE 的默认 DNS 后缀是 *internal-contoso.com*，则连接到 *https://some-random-app.internal-contoso.com* 需要对 **.internal-contoso.com* 有效的 SSL 证书。 

可通过各种方式获取有效的 SSL 证书，包括内部 CA、向外部颁发者购买证书，以及使用自签名证书。  无论 SSL 证书的来源如何，都需要正确配置以下证书属性：

* *使用者*：此属性必须设置为 **.your-root-domain-here.com*
* *使用者可选名称*：此属性必须同时包含 **.your-root-domain-here.com* 和 **scm.your-root-domain-here.com*。  输入第二项是因为将使用 *your-app-name.scm.your-root-domain-here.com* 形式的地址，建立与每个应用关联的 SCM/Kudu 站点的 SSL 连接。

备妥有效的 SSL 证书还需要两个额外的准备步骤。  SSL 证书必须转换/另存为 .pfx 文件。  记住，.pfx 文件必须包含所有中间证书和根证书，还必须使用密码保护。

然后必须将生成的 .pfx 文件转换为 base64 字符串，因为会使用 Azure Resource Manager 模板上传 SSL 证书。  Azure Resource Manager 模板是文本文件，因此必须将 .pfx 文件转换为 base64 字符串，才能纳入为模板的参数。

以下 Powershell 代码片段显示生成自签名证书、将证书导出为 .pfx 文件、将 .pfx 文件转换为 base64 编码字符串，然后将 base64 编码字符串保存到一个单独文件的示例。  base64 编码的 Powershell 代码改写自 [Powershell 脚本博客][examplebase64encoding]。

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

成功生成 SSL 证书并转换为 base64 编码字符串后，GitHub 上的示例 Azure Resource Manager 模板即可用于[配置默认 SSL 证书][quickstartconfiguressl]。

*azuredeploy.parameters.json* 文件中的参数如下所列：

* *appServiceEnvironmentName*：要配置的 ILB ASE 的名称。
* *existingAseLocation*：包含 ILB ASE 部署所在的 Azure 区域的文本字符串。  例如：“美国中南部”。
* *pfxBlobString*：.pfx 文件的 based64 编码字符串表示形式。  使用前面所示的代码片段，复制并粘贴“exportedcert.pfx.b64”中包含的字符串，作为 *pfxBlobString* 属性的值。
* *password*：用于保护 .pfx 文件的密码。
* *certificateThumbprint*：证书的指纹。  如果从 Powershell 检索到此值（例如先前代码片段中的 *$certificate.Thumbprint*），可以按原样使用此值。  不过，如果从 Windows 证书对话框复制此值，请记得去除多余的空格。  *certificateThumbprint* 应如下所示：AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName*：用户自己选择的易记字符串标识符，用于标识证书。  此名称用作 *Microsoft.Web/certificates* 实体（表示 SSL 证书）的 Azure Resource Manager 唯一标识符的一部分。  名称**必须**以下述后缀结尾：\_yourASENameHere_InternalLoadBalancingASE。  此后缀由门户使用，表示证书用于维护启用 ILB 的 ASE 的安全。

*azuredeploy.parameters.json* 的缩写示例如下所示：

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

一旦填入 *azuredeploy.parameters.json* 文件，就可以使用以下 Powershell 代码片段配置默认 SSL 证书。  更改文件 PATH，以匹配 Azure Resource Manager 模板文件在计算机上的位置。  此外，切记提供自己的 Azure Resource Manager 部署名称值和资源组名称值。

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

提交 Azure Resource Manager 模板后，每个 ASE 前端需要大约 40 分钟来应用更改。  例如，有一个默认大小的 ASE 使用两个前端，则模板需要大约 1 小时 20 分钟才能完成。  运行模板时无法缩放 ASE。  

完成模板后，可通过 HTTPS 访问 ILB ASE 上的应用，并使用默认 SSL 证书保护连接。  如果 ILB ASE 上的应用使用应用程序名称与默认主机名的组合来寻址，则会使用默认 SSL 证书。  例如 *https://mycustomapp.internal-contoso.com* 会使用 **.internal-contoso.com* 的默认 SSL 证书。

不过，与公共多租户服务上运行的应用一样，开发人员也可以为单个应用配置自定义主机名，然后为单个应用配置唯一的 SNI SSL 证书绑定。  

## <a name="asev1"></a>ASEv1 ##
应用服务环境有两个版本：ASEv1 和 ASEv2。 上述信息重点介绍 ASEv2。 本部分说明 ASEv1 和 ASEv2 之间的差异。

在 ASEv1 中，需手动管理所有资源。 它们包括前端、辅助角色和基于 IP 的 SSL 所用的 IP 地址。 扩大应用服务计划之前，需要先扩大要托管该计划的辅助角色池。

ASEv1 使用与 ASEv2 不同的定价模型。 在 ASEv1中，需要为分配的每个内核付费。 包括未托管任何工作负荷的前端或辅助角色所使用的内核。 在 ASEv1中，应用服务环境的默认最大规模为 55 个主机总数。 其中包括辅助角色和前端。 ASEv1 的一项优势是可在经典虚拟网络和 Resource Manager 虚拟网络中进行部署。 若要深入了解 ASEv1，可查看：[应用服务环境 v1 简介][ASEv1Intro]

若要使用 Resource Manager 模板创建 ASEv1，可首先[使用 Resource Manager 模板创建 ILB ASE v1][ILBASEv1Template]。


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
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: ../../app-service-web/app-service-app-service-environment-create-ilb-ase-resourcemanager.md

