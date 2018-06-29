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
ms.date: 05/18/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: b5adc1bb5a5aae96f37cc312588aa71e57d8342e
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2018
ms.locfileid: "37083220"
---
# <a name="azure-stack-certificates-signing-request-generation"></a>Azure Stack 证书签名请求生成

可[从 PowerShell 库](https://aka.ms/AzsReadinessChecker)获取本文所述的 Azure Stack 就绪性检查器工具。 该工具可创建适用于 Azure Stack 部署的证书签名请求 (CSR)。 应该花费足够的时间来请求、生成并验证证书，以便在部署之前进行测试。

Azure Stack 就绪性检查器工具 (AzsReadinessChecker) 执行以下证书请求：

 - **标准证书请求**  
    根据[为 Azure Stack 部署生成 PKI 证书](azure-stack-get-pki-certs.md)执行请求。
 - **平台即服务**  
    （可选）根据 [Azure Stack 公钥基础结构证书要求 - 可选的 PaaS 证书](azure-stack-pki-certs.md#optional-paas-certificates)中的规定，请求证书的平台即服务 (PaaS) 名称。



## <a name="prerequisites"></a>必备组件

在为 Azure Stack 部署生成 PKI 证书的 CSR 之前，系统应符合以下先决条件：

 - Microsoft Azure 堆栈准备情况检查程序
 - 证书属性：
    - 区域名称
    - 外部完全限定的域名 (FQDN)
    - 主题
 - Windows 10 或 Windows Server 2016
 
  > [!NOTE]
  > 从证书颁发机构收回证书时，需要在同一个系统上完成[准备 Azure Stack PKI 证书](azure-stack-prepare-pki-certs.md)中的步骤！

## <a name="generate-certificate-signing-requests"></a>生成证书签名请求

使用以下步骤来准备和验证 Azure Stack PKI 证书： 

1.  在 PowerShell 提示符（5.1 或更高版本）下，运行以下 cmdlet 安装 AzsReadinessChecker：

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ````

2.  将**使用者**声明为有序字典。 例如： 

    ````PowerShell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"} 
    ````
    > [!note]  
    > 如果提供公用名 (CN)，此值将被证书请求的第一个 DNS 名称覆盖。

3.  声明已存在的输出目录。 例如：

    ````PowerShell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ````
4.  声明标识系统

    Azure Active Directory

    ```PowerShell
    $IdentitySystem = "AAD"
    ````

    Active Directory 联合身份验证服务

    ```PowerShell
    $IdentitySystem = "ADFS"
    ````

5. 声明用于 Azure Stack 部署的**区域名称**和**外部 FQDN**。

    ```PowerShell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ````

    > [!note]  
    > `<regionName>.<externalFQDN>` 构成了 Azure Stack 中所有外部 DNS 名称创建位置的基础，在此示例中，门户是 `portal.east.azurestack.contoso.com`。  

6. 若要生成证书签名请求每个 DNS 名称：

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ````

    若要包括 PaaS 服务，请指定开关 ```-IncludePaaS```

7. 或者，对于开发/测试环境。 若要生成多个使用者备用名称的单个证书请求将添加 **-RequestType SingleCSR**参数和值 (**不**建议用于生产环境):

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ````

    若要包括 PaaS 服务，请指定开关 ```-IncludePaaS```
    
8. 查看输出：

    ````PowerShell  
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Request Generation

    CSR generating for following SAN(s): dns=*.east.azurestack.contoso.com&dns=*.blob.east.azurestack.contoso.com&dns=*.queue.east.azurestack.contoso.com&dns=*.table.east.azurestack.cont
    oso.com&dns=*.vault.east.azurestack.contoso.com&dns=*.adminvault.east.azurestack.contoso.com&dns=portal.east.azurestack.contoso.com&dns=adminportal.east.azurestack.contoso.com&dns=ma
    nagement.east.azurestack.contoso.com&dns=adminmanagement.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\username\Documents\AzureStackCSR\wildcard_east_azurestack_contoso_com_CertRequest_20180405233530.req
    Certreq.exe output: CertReq: Request Created

    Finished Certificate Request Generation

    AzsReadinessChecker Log location: C:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\1.1803.405.3\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ````

9.  将生成的 **.REQ** 文件提交到 CA（内部或公共 CA）。  **Start-AzsReadinessChecker** 的输出目录包含提交到证书颁发机构时所需的 CSR。  它还包含一个子目录，其中包含生成证书请求期间用作参考的 INF 文件。 请确保 CA 使用符合 [Azure Stack PKI 要求](azure-stack-pki-certs.md)的生成请求来生成证书。

## <a name="next-steps"></a>后续步骤

[准备 Azure Stack PKI 证书](azure-stack-prepare-pki-certs.md)
