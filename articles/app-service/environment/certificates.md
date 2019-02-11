---
title: 证书和应用服务环境 – Azure
description: 解释与 ASE 上的证书相关的多个主题
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 9e21a7e4-2436-4e81-bb05-4a6ba70eeaf7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: bcb0c806d916b9dff4461cad829a1d75e8df7cf6
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271889"
---
# <a name="certificates-and-the-app-service-environment"></a>证书和应用服务环境 

应用服务环境 (ASE) 是在 Azure 虚拟网络 (VNet) 中运行的 Azure 应用服务的部署。 可以使用能够通过 Internet 访问的应用程序终结点或者 VNet 中的应用程序终结点来部署 ASE。 如果使用可通过 Internet 访问的终结点部署 ASE，则该部署称为外部 ASE。 如果使用 VNet 中的终结点部署 ASE，则该部署称为 ILB ASE。 可以在[创建和使用 ILB ASE](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase) 文档中详细了解 ILB ASE。

ASE 是单租户系统。 由于它是单一租户，某些只能在 ASE 中使用的功能不能在多租户应用服务中使用。 

## <a name="ilb-ase-certificates"></a>ILB ASE 证书 

如果使用外部 ASE，则可以通过 [应用名称].[ASE 名称].p.azurewebsites.net 访问应用。 默认情况下，所有 ASE 甚至 ILB ASE，都是使用遵循该格式的证书创建的。 创建 ILB ASE 后，可以基于创建 ILB ASE 时指定的域名来访问应用。 为使应用支持 SSL，需要上传证书。 可通过三种方式获取有效的 SSL 证书：使用内部证书颁发机构、向外部颁发者购买证书或使用自签名证书。 

可以使用两个选项配置 ILB ASE 的证书。  可为 ILB ASE 设置通配符默认证书，或者在 ASE 中的单个 Web 应用上设置证书。  无论做出哪种选择，都必须正确配置以下证书属性：

- **使用者：** 对于通配符 ILB ASE 证书，此属性必须设置为 *.[根域]。 如果为应用创建证书，则此属性应是 [应用名称].[根域]
- **使用者可选名称：** 此属性必须同时包括通配符 ILB ASE 证书的 *.[根域] 和 *.scm.[根域]。 如果为应用创建证书，则此属性应是 [应用名称].[根域] 和 [应用名称].scm.[根域]

作为第三种变体，可以创建在证书 SAN 中包含所有应用名称的 ILB ASE 证书，而不使用通配符引用。 此方法的问题在于，需要事先知道要放入 ASE 的应用名称，或者需要不断更新 ILB ASE 证书。

### <a name="upload-certificate-to-ilb-ase"></a>将证书上传到 ILB ASE 

在门户中创建 ILB ASE 之后，必须为 ILB ASE 设置证书。 在设置证书之前，ASE 将显示一个横幅，指出未设置证书。  

上传的证书必须是 .pfx 文件。 上传证书后，ASE 将执行缩放操作以设置证书。 

无法在门户中一次性创建 ASE 并上传证书，即使在一个模板中也无法做到这一点。 作为单独的操作，可以使用[从模板创建 ASE](./create-from-template.md) 文档中所述的模板上传证书。  

若要快速创建自签名证书用于测试，可以使用以下 PowerShell 代码：

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     


## <a name="application-certificates"></a>应用程序证书 

在 ASE 中托管的应用可以使用多租户应用服务支持的以应用为中心的证书功能。 这些功能包括：  

- SNI 证书 
- 基于 IP 的 SSL，仅在外部 ASE 中受支持。  ILB ASE 不支持基于 IP 的 SSL。
- KeyVault 托管的证书 

应用服务 SSL 教程 https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl 中提供了有关上传和管理这些证书的说明。  如果只需将证书配置为与分配到 Web 应用的自定义域名相匹配，则遵照这些说明操作即可。 若要上传使用默认域名的 ILB ASE Web 应用的证书，则需要根据前文所述，在证书的 SAN 中指定 scm 站点。 

## <a name="tls-settings"></a>TLS 设置 

可在应用级别配置 TLS 设置。  

## <a name="private-client-certificate"></a>专用客户端证书 

一个常见用例是将应用配置为客户端-服务器模型中的客户端。 如果使用专用 CA 证书保护服务器，则需要将客户端证书上传到应用。  以下说明介绍如何将证书加载到运行应用的辅助角色的信任存储中。 如果将证书加载到一个应用，则可以将其用于同一应用服务计划中的其他应用，而无需再次上传该证书。

将证书上传到 ASE 中的应用：

1. 生成证书的 *.cer* 文件。 
2. 在 Azure 门户中转到需要该证书的应用
3. 转到该应用中的 SSL 设置。 单击“上传证书”。 选择“公共”。 选择“本地计算机”。 提供一个名称。 浏览并选择你的 *.cer* 文件。 选择“上传”。 
4. 复制指纹。
5. 转到“应用程序设置”。 创建应用设置 WEBSITE_LOAD_ROOT_CERTIFICATES，并使用指纹作为值。 如果有多个证书，可将其放到同一个设置中，并用逗号分隔（不要包含空格），例如 

    84EC242A4EC7957817B8E48913E50953552DAFA6,6A5C65DC9247F762FE17BF8D4906E04FE6B31819

配置了该设置的应用所在的同一个应用服务计划中的所有应用都可以使用该证书。 如果需要将该证书提供给不同应用服务计划中的应用使用，则需要在该应用服务计划中的应用上重复“应用设置”操作。 若要检查是否设置了证书，请转到 Kudu 控制台，并在 PowerShell 调试控制台中发出以下命令：dir cert: \localmachine\root。 

若要执行测试，可以创建自签名证书，并使用以下 PowerShell 命令生成 *.cer* 文件： 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.cer"
    export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT

