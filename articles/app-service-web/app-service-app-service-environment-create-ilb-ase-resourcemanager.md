---
title: "如何使用 Azure Resource Manager 模板创建 ILB ASE | Microsoft Docs"
description: "了解如何使用 Azure Resource Manager 模板创建内部负载平衡器 ASE。"
services: app-service
documentationcenter: 
author: stefsch
manager: nirma
editor: 
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2016
ms.author: stefsch
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 33d1638dfbf1e314d7ea298a39dd4e646c3faf10


---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>如何使用 Azure Resource Manager 模板创建 ILB ASE
## <a name="overview"></a>概述
使用虚拟网络内部地址（而不是公用 VIP）可以创建应用服务环境。  此内部地址由称为内部负载平衡器 (ILB) 的 Azure 组件提供。  使用 Azure 门户可以创建 ILB ASE。  也可以通过 Azure Resource Manager 模板使用自动化功能创建。  本文介绍了使用 Azure Resource Manager 模板创建 ILB ASE 所需的步骤和语法。

自动创建 ILB ASE 涉及三个步骤：

1. 首先，使用内部负载平衡器地址（而不是公用 VIP）在虚拟网络中创建基础 ASE。  在此步骤中，将为 ILB ASE 分配根域名称。
2. 一旦创建 ILB ASE，就会上传 SSL 证书。  
3. 上传的 SSL 证书显式分配给 ILB ASE 作为其“默认”SSL 证书。  如果应用使用分配给 ASE 的一般根域（例如 https://someapp.mycustomrootcomain.com）来寻址，此 SSL 证书将用于 ILB ASE 上应用的 SSL 流量

## <a name="creating-the-base-ilb-ase"></a>创建基础 ILB ASE
GitHub 上（[此处][quickstartilbasecreate]）提供了示例 Azure Resource Manager 模板及其相关联的参数文件。

*azuredeploy.parameters.json* 文件中的大部分参数通用于创建 ILB ASE 以及绑定到公用 VIP 的 ASE。  创建 ILB ASE 时，以下列表会调出特殊注释的参数或唯一的参数：

* *interalLoadBalancingMode*：此属性多数情况下设置为 3，这表示端口 80/443 上的 HTTP/HTTPS 流量以及 ASE 上的 FTP 服务所侦听的控制/数据通道端口将绑定到 ILB 分配的虚拟网络内部地址。  如果此属性改设为 2，则只有与 FTP 服务相关的端口（控制和数据通道）会绑定到 ILB 地址，而 HTTP/HTTPS 流量将保留在公用 VIP 上。
* *dnsSuffix*：此参数定义要分配给 ASE 的默认根域。  在 Azure 应用服务的公共变体中，所有 Web 应用的默认根域均为 *azurewebsites.net*。  不过，ILB ASE 位于客户虚拟网络的内部，因此不适合使用公共服务的默认根域，  而应当具有适合在公司的内部虚拟网络中使用的默认根域。  例如，假定的 Contoso Corporation 可能会将 *internal-contoso.com* 的默认根域用于只能在 Contoso 虚拟网络内解析和访问的应用。 
* *ipSslAddressCount*：在 *azuredeploy.json* 文件中，此参数的值自动默认为 0，因为 ILB ASE 只有一个 ILB 地址。  ILB ASE 没有显式 IP-SSL 地址，因此 ILB ASE 的 IP-SSL 地址池必须设置为零，否则会发生预配错误。 

一旦针对 ILB ASE 填入 *azuredeploy.parameters.json* 文件，就可以使用以下 Powershell 代码片段创建 ILB ASE。  更改文件 PATH，以匹配 Azure Resource Manager 模板文件在计算机上的位置。  此外，切记提供自己的 Azure Resource Manager 部署名称值和资源组名称值。

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

提交 Azure Resource Manager 模板后，需要数小时才能创建 ILB ASE。  创建完成后，ILB ASE 显示在触发部署的订阅的应用服务环境列表的门户 UX 中。

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>上传和配置“默认”SSL 证书
创建 ILB ASE 后，SSL 证书应与 ASE 关联，作为用来建立应用的 SSL 连接的“默认”SSL 证书。  继续以假定的 Contoso Corporation 为例，如果 ASE 的默认 DNS 后缀是 *internal-contoso.com*，则连接到 *https://some-random-app.internal-contoso.com* 需要对 **.internal-contoso.com* 有效的 SSL 证书。 

可通过各种方式获取有效的 SSL 证书，包括内部 CA、向外部颁发者购买证书，以及使用自签名证书。  无论 SSL 证书的来源如何，都需要正确配置以下证书属性：

* *使用者*：此属性必须设置为 **.your-root-domain-here.com*
* *使用者可选名称*：此属性必须同时包含 **.your-root-domain-here.com* 和 **.scm.your-root-domain-here.com*。  输入第二项是因为将使用 *your-app-name.scm.your-root-domain-here.com* 形式的地址，建立与每个应用关联的 SCM/Kudu 站点的 SSL 连接。

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

成功生成 SSL 证书并转换为 base64 编码字符串后，GitHub 上的示例 Azure Resource Manager 模板即可用于[配置默认 SSL 证书][configuringDefaultSSLCertificate]。

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

## <a name="getting-started"></a>入门
若要开始使用应用服务环境，请参阅 [Introduction to App Service Environment](app-service-app-service-environment-intro.md)（应用服务环境简介）

[README for Application Service Environments](../app-service/app-service-app-service-environments-readme.md)（应用程序服务环境自述文件）中提供了有关应用服务环境的所有文章和操作说明。

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 




<!--HONumber=Nov16_HO3-->


