---
title: 为 Azure Stack 集成系统部署生成 Azure Stack 公钥基础结构证书 | Microsoft Docs
description: 介绍 Azure Stack 集成系统的 Azure Stack PKI 证书部署过程。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: fc2ec96113310f54d32a67ea5fa31725600046c9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="generate-pki-certificates-for-azure-stack-deployment"></a>为 Azure Stack 部署生成 PKI 证书
既然已了解 Azure Stack 部署的 [PKI 证书要求](azure-stack-pki-certs.md)，需要从所选证书颁发机构 (CA) 获取这些证书。 

## <a name="request-certificates-using-an-inf-file"></a>使用 INF 文件请求证书
从公共 CA 或内部 CA 请求证书的一种方法是使用 INF 文件。 Windows 内置 certreq.exe 实用程序可使用指定证书详细信息的 INF 文件生成请求文件，如本部分中所述。 

### <a name="sample-inf-file"></a>示例 INF 文件 
示例证书请求 INF 文件可用于创建要提交到 CA（内部或公共）的脱机证书请求文件。 INF 在单个通配符证书中涵盖所有所需的终结点（包括可选的 PaaS 服务）。 

示例 INF 文件假定区域等于 **sea**，外部 FQDN 值为 **sea&#46;contoso&#46;com**。生成用于部署的 .INF 文件前，请更改这些值以符合你的环境。 

    
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
    

## <a name="generate-and-submit-request-to-the-ca"></a>生成请求并将其提交到 CA
以下工作流介绍如何自定义先前生成的示例 INF 文件并使用它从 CA 请求证书：

1. **编辑并保存 INF 文件**。 复制所提供的示例，并将其保存到一个新文本文件中。 将使用者名称和外部 FQDN 替换为匹配你的部署的值，并将该文件另存为 .INF 文件。
2. **使用 certreq 生成请求**。 使用 Windows 计算机，以管理员身份启动命令提示符并运行以下命令生成请求 (.req) 文件：`certreq -new <yourinffile>.inf <yourreqfilename>.req`。
3. **提交到 CA**。 将生成的 .REQ 文件提交到 CA（可以是内部 CA 或公共 CA）。
4. **导入 .CER**。 CA 将返回一个 .CER 文件。 使用从中生成请求文件的同一 Windows 计算机，将返回的 .CER 文件导入到计算机/个人存储。 
5. **导出 .PFX 并将其复制到部署文件夹**。 将证书（包括私钥）导出为 .PFX 文件，并将该 .PFX 文件复制到 [Azure Stack 部署 PKI 要求](azure-stack-pki-certs.md)中所述的部署文件夹。

## <a name="next-steps"></a>后续步骤
[准备 Azure Stack PKI 证书](azure-stack-prepare-pki-certs.md)
