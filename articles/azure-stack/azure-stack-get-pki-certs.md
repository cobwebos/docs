---
title: 为 Azure Stack 集成系统部署生成 Azure Stack 公钥基础结构证书 | Microsoft Docs
description: 介绍 Azure 堆栈集成系统的 Azure 堆栈 PKI 证书部署过程。
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
ms.date: 04/26/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: cbc1efaee7404c3ffc82acea0846136c43eba2a9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="azure-stack-certificates-signing-request-generation"></a>Azure 堆栈证书签名请求生成

在本文中所述的 Azure 堆栈准备情况检查程序工具可[从 PowerShell 库](https://aka.ms/AzsReadinessChecker)。 该工具创建证书签名请求 (Csr) 适用于 Azure 堆栈部署。 证书应请求、 生成，和足够的时间来测试部署前通过验证。 

Azure 堆栈准备情况检查器工具 (AzsReadinessChecker) 执行的以下证书申请：

 - **标准证书请求**  
    根据请求[生成 Azure 堆栈部署的 PKI 证书](azure-stack-get-pki-certs.md)。 
 - **请求类型**  
    请求多个通配符 SAN，多个域证书，一个通配符证书请求。
 - **平台即服务**  
    根据需要请求中指定的证书的平台即服务 (PaaS) 名称[Azure 堆栈公钥基础结构证书要求的可选 PaaS 证书](azure-stack-pki-certs.md#optional-paas-certificates)。

## <a name="prerequisites"></a>必备组件

你的系统应在生成 Azure 堆栈部署的 PKI 证书 CSR(s) 之前满足以下先决条件：

 - Microsoft Azure 堆栈准备情况检查程序
 - 证书属性：
    - 区域名称
    - 外部完全限定的域名 (FQDN)
    - 主题
 - Windows 10 或 Windows Server 2016

## <a name="generate-certificate-signing-requests"></a>生成证书签名请求

使用以下步骤来准备和验证 Azure Stack PKI 证书： 

1.  通过运行以下 cmdlet 从 PowerShell 提示符下 （5.1 或更高版本），安装 AzsReadinessChecker:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ````

2.  声明**主题**作为有序的字典。 例如： 

    ````PowerShell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"} 
    ````
    > [!note]  
    > 如果提供的公用名 (CN)，则这将覆盖由证书请求的第一个 DNS 名称。

3.  声明的输出目录中已存在：

    ````PowerShell  
    $outputDirectory = "$ENV:USERNAME\Documents\AzureStackCSR" 
    ````

4. 声明**区域名称**和**外部 FQDN**适用于 Azure 堆栈部署。

    ```PowerShell  
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ````

    > [!note]  
    > `<regionName>.<externalFQDN>` 窗体在其 Azure 堆栈中的所有外部 DNS 名称创建，在此示例中的基础，门户将会`portal.east.azurestack.contoso.com`。

5. 若要使用多个使用者备用名称包括那些需要 PaaS 服务生成的单个证书请求：

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleCSR -OutputRequestPath $OutputDirectory -IncludePaaS
    ````

6. 若要生成单个证书签名请求不使用 PaaS 服务的每个 DNS 名称：

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType MultipleCSR -OutputRequestPath $OutputDirectory
    ````

7. 查看的输出：

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

8.  提交**。请求**到你的 CA （内部或公共） 生成的文件。  输出目录**开始 AzsReadinessChecker**包含 CSR(s) 必要以便提交至证书颁发机构。  它还包含子目录包含在证书请求生成，作为引用过程中使用的 INF 文件。 请确保你的 CA 会生成使用生成的请求的证书，满足[Azure 堆栈的 PKI 要求](azure-stack-pki-certs.md)。

## <a name="next-steps"></a>后续步骤

[准备 Azure Stack PKI 证书](azure-stack-prepare-pki-certs.md)

