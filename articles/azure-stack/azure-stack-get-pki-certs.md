---
title: "生成集成的 Azure 堆栈系统部署的 Azure 堆栈公钥基础结构证书 |Microsoft 文档"
description: "介绍 Azure 堆栈 PKI 证书部署 processfor 集成的 Azure 堆栈系统。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: jeffgilb
ms.reviewer: ppacent
ms.openlocfilehash: 991a94e4ca41bad438a3c8d06e4e1f691cff91bc
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="generate-pki-certificates-for-azure-stack-deployment"></a>生成 Azure 堆栈部署的 PKI 证书
既然你知道[的 PKI 证书要求](azure-stack-pki-certs.md)对于 Azure 堆栈部署，你需要从所选证书颁发机构 (CA) 获取这些证书。 

## <a name="request-certificates-using-an-inf-file"></a>使用 INF 文件申请证书
从公共 CA 或内部 CA 请求证书的一种方法是使用 INF 文件。 Windows 内置 certreq.exe 实用程序可用于 INF 文件指定证书的详细信息，生成一个请求文件，如本部分中所述。 

### <a name="sample-inf-file"></a>示例 INF 文件 
示例证书请求 INF 文件可以用于创建以提交到 CA 的脱机证书请求文件 （内部或公共）。 INF 详细介绍所有所需的终结点 （包括可选的 PaaS 服务） 的单个通配符证书。 

示例 INF 文件假定该区域是等于**sea**和外部的 FQDN 值是**sea &#46; contoso &#46; com**。更改这些值，以在生成前匹配你的环境。你的部署的 INF 文件。 

    
    [Version] 
    Signature="$Windows NT$"

    [NewRequest] 
    Subject = "C=US, O=Microsoft, L=Redmond, ST=Washington, CN=portal.sea.contoso.com"

    Exportable = TRUE                   ; Private key is not exportable 
    KeyLength = 2048                    ; Common key sizes: 512, 1024, 2048, 4096, 8192, 16384 
    KeySpec = 1                         ; AT_KEYEXCHANGE 
    KeyUsage = 0xA0                     ; Digital Signature, Key Encipherment 
    MachineKeySet = True                ; The key belongs to the local computer account 
    ProviderName = "Microsoft RSA SChannel Cryptographic Provider" 
    ProviderType = 12 
    SMIME = FALSE 
    RequestType = PKCS10
    HashAlgorithm = SHA256

    ; At least certreq.exe shipping with Windows Vista/Server 2008 is required to interpret the [Strings] and [Extensions] sections below

    [Strings] 
    szOID_SUBJECT_ALT_NAME2 = "2.5.29.17" 
    szOID_ENHANCED_KEY_USAGE = "2.5.29.37" 
    szOID_PKIX_KP_SERVER_AUTH = "1.3.6.1.5.5.7.3.1" 
    szOID_PKIX_KP_CLIENT_AUTH = "1.3.6.1.5.5.7.3.2"

    [Extensions] 
    %szOID_SUBJECT_ALT_NAME2% = "{text}dns=*.sea.contoso.com&dns=*.blob.sea.contoso.com&dns=*.queue.sea.contoso.com&dns=*.table.sea.contoso.com&dns=*.vault.sea.contoso.com&dns=*.adminvault.sea.contoso.com&dns=*.dbadapter.sea.contoso.com&dns=*.appservice.sea.contoso.com&dns=*.scm.appservice.sea.contoso.com&dns=api.appservice.sea.contoso.com&dns=ftp.appservice.sea.contoso.com&dns=sso.appservice.sea.contoso.com&dns=adminportal.sea.contoso.com&dns=management.sea.contoso.com&dns=adminmanagement.sea.contoso.com" 
    %szOID_ENHANCED_KEY_USAGE% = "{text}%szOID_PKIX_KP_SERVER_AUTH%,%szOID_PKIX_KP_CLIENT_AUTH%"

    [RequestAttributes]
    

## <a name="generate-and-submit-request-to-the-ca"></a>生成并向 CA 提交申请
下面的工作流描述如何自定义和使用先前生成从 CA 请求证书的示例 INF 文件：

1. **编辑并保存 INF 文件**。 复制此示例提供，并将其保存到一个新的文本文件。 使用者名称和外部 FQDN 替换为匹配你的部署，并将文件另存的值。INF 文件。
2. **生成使用 certreq 请求**。 使用的 Windows 计算机，启动管理员命令提示符并运行以下命令以生成一个请求 (.req) 文件： `certreq -new <yourinffile>.inf <yourreqfilename>.req`。
3. **向 CA 提交**。 提交。请求到 CA （可以是内部或公共） 生成文件。
4. **导入。CER**。 CA 返回。CER 文件。 使用从其生成的申请文件的相同 Windows 计算机，导入。CER 文件返回到的计算机/个人存储中。 
5. **导出并复制。到部署文件夹的 PFX**。 为导出的证书 （包括私钥）。PFX 文件中，并复制。到部署文件夹中所述的 PFX 文件[Azure 堆栈部署 PKI 要求](azure-stack-pki-certs.md)。

## <a name="next-steps"></a>后续步骤
[准备 Azure 堆栈 PKI 证书](prepare-pki-certs.md)