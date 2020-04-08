---
title: 创建 ILB ASE v1
description: 使用内部负载均衡器 （ILB ASE） 创建应用服务环境。 此文档仅提供给使用旧版 v1 ASE 的客户。
author: stefsch
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: f05780610a2a6033b069721b143aca5e5efa6c35
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804514"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>如何使用 Azure 资源管理器模板创建 ILB ASE

> [!NOTE] 
> 本文介绍应用服务环境 v1。 应用服务环境有一个较新版本，此版本更易于使用并在更强大的基础结构上运行。 要了解有关新版本的更多，从[应用服务环境简介](intro.md)开始。
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>概述
使用虚拟网络内部地址（而不是公用 VIP）可以创建应用服务环境。  此内部地址由称为内部负载均衡器 (ILB) 的 Azure 组件提供。  使用 Azure 门户可以创建 ILB ASE。  也可以通过 Azure 资源管理器模板使用自动化功能创建。  本文介绍了使用 Azure 资源管理器模板创建 ILB ASE 所需的步骤和语法。

自动创建 ILB ASE 涉及三个步骤：

1. 首先，使用内部负载均衡器地址（而不是公用 VIP）在虚拟网络中创建基础 ASE。  在此步骤中，将为 ILB ASE 分配根域名称。
2. 创建 ILB ASE 后，将上载 TLS/SSL 证书。  
3. 上传的 TLS/SSL 证书被显式分配给 ILB ASE 作为其"默认"TLS/SSL 证书。  当应用使用分配给 ASE 的通用根域寻址时，此 TLS/SSL 证书将用于对 ILB ASE 上应用的 TLS 流量`https://someapp.mycustomrootcomain.com`（例如）

## <a name="creating-the-base-ilb-ase"></a>创建基础 ILB ASE
GitHub（[此处][quickstartilbasecreate]）上提供了 Azure 资源管理器模板示例及其相关联的参数文件。

*azuredeploy.parameters.json* 文件中的大部分参数通用于创建 ILB ASE 以及绑定到公用 VIP 的 ASE。  创建 ILB ASE 时，以下列表会调出特殊注释的参数或唯一的参数：

* *内部负载平衡模式*：在大多数情况下，将其设置为 3，这意味着端口 80/443 上的 HTTP/HTTPS 流量以及 ASE 上的 FTP 服务侦听的控制/数据通道端口都将绑定到 ILB 分配的虚拟网络内部地址。  如果此属性改设为 2，则只有与 FTP 服务相关的端口（控制和数据通道）会绑定到 ILB 地址，而 HTTP/HTTPS 流量将保留在公用 VIP 上。
* *dnsSuffix*：此参数定义要分配给 ASE 的默认根域。  在 Azure 应用服务的公共变体中，所有 Web 应用的默认根域均为 *azurewebsites.net*。  不过，ILB ASE 位于客户虚拟网络的内部，因此不适合使用公共服务的默认根域，  而应当具有适合在公司的内部虚拟网络中使用的默认根域。  例如，假定的 Contoso Corporation 可能会将 *internal-contoso.com* 的默认根域用于只能在 Contoso 虚拟网络内解析和访问的应用。 
* *ipSslAddressCount*：在 *azuredeploy.json* 文件中，此参数的值自动默认为 0，因为 ILB ASE 只有一个 ILB 地址。  ILB ASE 没有显式 IP-SSL 地址，因此 ILB ASE 的 IP-SSL 地址池必须设置为零，否则会发生预配错误。 

一旦针对 ILB ASE 填入 *azuredeploy.parameters.json* 文件，就可以使用以下 Powershell 代码片段创建 ILB ASE。  更改文件 PATH，以匹配 Azure 资源管理器模板文件在计算机上的位置。  此外，切记提供自己的 Azure 资源管理器部署名称值和资源组名称值。

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

提交 Azure 资源管理器模板后，需要数小时才能创建 ILB ASE。  创建完成后，ILB ASE 显示在触发部署的订阅的应用服务环境列表的门户 UX 中。

## <a name="uploading-and-configuring-the-default-tlsssl-certificate"></a>上传和配置"默认"TLS/SSL 证书
创建 ILB ASE 后，TLS/SSL 证书应与 ASE 关联，作为用于建立与应用的 TLS/SSL 连接的"默认"TLS/SSL 证书。  继续假设的 Contoso 公司示例，如果 ASE 的默认 DNS 后缀*internal-contoso.com*，则连接*https://some-random-app.internal-contoso.com*需要对 **.internal-contoso.com*有效的 TLS/SSL 证书的连接。 

有多种方法可以获取有效的 TLS/SSL 证书，包括内部 C、从外部颁发者处购买证书以及使用自签名证书。  无论 TLS/SSL 证书的来源如何，都需要正确配置以下证书属性：

* *使用者*：此属性必须设置为 **.your-root-domain-here.com*
* *使用者可选名称*：此属性必须同时包含 **.your-root-domain-here.com* 和 **scm.your-root-domain-here.com*。  第二个条目的原因是，TLS 连接到与每个应用关联的 SCM/Kudu 站点将使用表单*的地址your-app-name.scm.your-root-domain-here.com*。

手头有有效的 TLS/SSL 证书，还需要执行两个额外的准备步骤。  TLS/SSL 证书需要转换/保存为 .pfx 文件。  记住，.pfx 文件必须包含所有中间证书和根证书，还必须使用密码保护。

然后，产生的 .pfx 文件需要转换为 base64 字符串，因为 TLS/SSL 证书将使用 Azure 资源管理器模板上载。  Azure 资源管理器模板是文本文件，因此必须将 .pfx 文件转换为 base64 字符串，才能纳入为模板的参数。

以下 Powershell 代码片段显示生成自签名证书、将证书导出为 .pfx 文件、将 .pfx 文件转换为 base64 编码字符串，然后将 base64 编码字符串保存到一个单独文件的示例。  base64 编码的 Powershell 代码改写自 [Powershell 脚本博客][examplebase64encoding]。

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

成功生成 TLS/SSL 证书并将其转换为 base64 编码字符串后，可以使用 GitHub 上[用于配置默认 TLS/SSL 证书的示例][configuringDefaultSSLCertificate]Azure 资源管理器模板。

*azuredeploy.parameters.json* 文件中的参数如下所列：

* *appServiceEnvironmentName*：要配置的 ILB ASE 的名称。
* *existingAseLocation*：包含 ILB ASE 部署所在的 Azure 区域的文本字符串。  例如：“美国中南部”。
* *pfxBlobString*：.pfx 文件的 based64 编码字符串表示形式。  使用前面所示的代码片段，复制并粘贴“exportedcert.pfx.b64”中包含的字符串，作为 *pfxBlobString* 属性的值。
* *密码*：用于保护 .pfx 文件的密码。
* *certificateThumbprint*：证书的指纹。  如果从 Powershell 检索到此值（例如先前代码片段中的 *$certificate.Thumbprint*），可以按原样使用此值。  不过，如果从 Windows 证书对话框复制此值，请记得去除多余的空格。  *certificateThumbprint* 应如下所示：AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName*：用户自己选择的易记字符串标识符，用于标识证书。  该名称用作表示 TLS/SSL 证书的*Microsoft.Web/证书*实体的唯一 Azure 资源管理器标识符的一部分。  名称**必须**以下述后缀结尾：\_yourASENameHere_InternalLoadBalancingASE。  此后缀由门户使用，表示证书用于维护启用 ILB 的 ASE 的安全。

*azuredeploy.parameters.json* 的缩写示例如下所示：

    {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
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

填写*azuredeploy.parameters.json*文件后，可以使用以下 Powershell 代码段配置默认 TLS/SSL 证书。  更改文件 PATH，以匹配 Azure 资源管理器模板文件在计算机上的位置。  此外，切记提供自己的 Azure 资源管理器部署名称值和资源组名称值。

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

提交 Azure 资源管理器模板后，每个 ASE 前端需要大约 40 分钟来应用更改。  例如，有一个默认大小的 ASE 使用两个前端，则模板需要大约 1 小时 20 分钟才能完成。  运行模板时无法缩放 ASE。  

模板完成后，可以通过 HTTPS 访问 ILB ASE 上的应用，并使用默认 TLS/SSL 证书保护连接。  当使用应用程序名称加上默认主机名的组合解决 ILB ASE 上的应用时，将使用默认 TLS/SSL 证书。  例如*https://mycustomapp.internal-contoso.com*，将使用默认 TLS/SSL 证书进行 **.internal-contoso.com*。

但是，与在公共多租户服务上运行的应用一样，开发人员还可以为各个应用配置自定义主机名，然后为各个应用配置唯一的 SNI TLS/SSL 证书绑定。  

## <a name="getting-started"></a>入门
若要开始使用应用服务环境，请参阅[应用服务环境简介](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

