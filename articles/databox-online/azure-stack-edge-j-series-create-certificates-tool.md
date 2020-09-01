---
title: 使用 Microsoft Azure Stack 集线器就绪检查程序工具创建证书 |Microsoft Docs
description: 介绍如何使用 Azure Stack 中心就绪检查程序工具创建证书请求，然后在 Azure Stack 边缘 GPU 设备上获取和安装证书。
services: Azure Stack Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: ea560cf7d61a208cf29aef977aea6d1584a7cdb7
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146040"
---
# <a name="create-certificates-for-your-azure-stack-edge-using-azure-stack-hub-readiness-checker-tool"></a>使用 Azure Stack 中心就绪检查程序工具为 Azure Stack 边缘创建证书 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

本文介绍如何使用 Azure Stack 中心就绪检查程序工具为 Azure Stack 边缘创建证书。 

## <a name="using-azure-stack-hub-readiness-checker-tool"></a>使用 Azure Stack 集线器就绪检查程序工具

使用 Azure Stack 集线器准备情况检查程序工具为 Azure Stack 边缘设备部署 (Csr) 创建证书签名请求。 你可以在为 Azure Stack Edge 设备放置订单并等待设备到达后创建这些请求。 

> [!NOTE]
> 此工具仅用于测试或开发目的，不适用于生产设备。 

你可以使用 Azure Stack 集线器就绪检查器工具 (AzsReadinessChecker) 来请求以下证书：

- Azure 资源管理器证书
- 本地 UI 证书
- 节点证书
- Blob 证书
- VPN 证书


## <a name="prerequisites"></a>先决条件

若要为 Azure Stack Edge 设备部署创建 Csr，请确保： 

- 你有运行 Windows 10 或 Windows Server 2016 或更高版本的客户端。 
- 已从此系统上 [的 PowerShell 库中](https://aka.ms/AzsReadinessChecker) 下载了 Microsoft Azure Stack 集线器就绪检查器工具1.2002.1133.85。 可能需要搜索此包。 只有此版本的工具才能为 Azure Stack 边缘设备创建证书。
- 你具有以下证书信息：
  - 设备名称
  - 节点序列号
  - 外部完全限定的域名 (FQDN)

## <a name="generate-certificate-signing-requests"></a>生成证书签名请求

使用以下步骤来准备 Azure Stack Edge 设备证书：

1. 以管理员身份运行 PowerShell (5.1 或更高版本) 。
2. 安装 Azure Stack 集线器就绪检查程序工具。 在 PowerShell 提示符下，键入： 

    ```azurepowershell
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker -RequiredVersion 1.2002.1133.85 -Force
    ```

    若要验证已安装的版本，请键入：  

    ```azurepowershell
    Get-InstalledModule -Name Microsoft.AzureStack.ReadinessChecker  | ft Name, Version 
    ```

3. 如果证书不存在，则为所有证书创建一个目录。 键入： 
    
    ```azurepowershell
    New-Item "C:\certrequest" -ItemType Directory
    ``` 
    
4. 若要创建证书请求，请提供以下信息。 如果要生成 VPN 证书，则其中某些输入不适用。 
    
    |输入 |说明  |
    |---------|---------|
    |`OutputRequestPath`|要在其中创建证书请求的本地客户端上的文件路径。        |
    |`DeviceName`|设备在设备的本地 web UI 的 " **设备** " 页中的名称。 <br> VPN 证书不需要此字段。         |
    |`NodeSerialNumber`|设备的本地 web UI 中 " **网络** " 页上的设备节点序列号。 <br> VPN 证书不需要此字段。       |
    |`ExternalFQDN`|设备本地 web UI 的 " **设备** " 页中的 DNSDomain 值。         |
    |`RequestType`|对于 `MultipleCSR` 各种终结点，请求类型可以是不同的证书，也可以是 `SingleCSR` 所有终结点的单个证书。 <br> VPN 证书不需要此字段。     |

    对于除 VPN 证书之外的所有证书，请键入： 
    
    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeDEVICE'
        DeviceName = 'myTEA1'
        NodeSerialNumber = 'VM1500-00025'
        externalFQDN = 'azurestackedge.contoso.com'
        requestType = 'MultipleCSR'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    如果要创建 VPN 证书，请键入： 

    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeVPN'
        externalFQDN = 'azurestackedge.contoso.com'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    
5. 你会在上面的 OutputRequestPath 参数中指定的目录下找到证书请求文件。 使用参数时 `MultipleCSR` ，将看到4个文件，其中包含 `.req` 扩展名。 文件如下所示：

    
    |文件名  |证书请求的类型  |
    |---------|---------|
    |从 `DeviceName`     |本地 web UI 证书请求      |
    |从 `NodeSerialNumber`     |节点证书请求         |
    |自 `login` 起     |Azure 资源管理器终结点证书请求       |
    |自 `wildcard` 起     |Blob 存储证书请求;它包含通配符，因为它涵盖了你可以在设备上创建的所有存储帐户。          |
    |自 `AzureStackEdgeVPNCertificate` 起     |VPN 客户端证书请求。         |

    还会看到一个 INF 文件夹。 这包含一个管理。 <边缘-devicename> 信息文件以明文形式说明证书详细信息。  


6. 将这些文件提交给证书颁发机构 (内部或公用) 。 确保 CA 使用生成的请求生成证书，该请求符合 [节点证书](azure-stack-edge-j-series-manage-certificates.md#node-certificates)、 [终结点证书](azure-stack-edge-j-series-manage-certificates.md#endpoint-certificates)和 [本地 UI 证书](azure-stack-edge-j-series-manage-certificates.md#local-ui-certificates)的 Azure Stack 边缘证书要求。

## <a name="prepare-certificates-for-deployment"></a>为部署准备证书

您从证书颁发机构获取的证书文件 (CA) 必须导入和导出，其属性与 Azure Stack Edge 设备证书要求相匹配。 在生成证书签名请求的同一系统上完成以下步骤。

- 若要导入证书，请按照在 [访问 Azure Stack Edge 设备的客户端上导入证书](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)中的步骤操作。

- 若要导出证书，请按照 [从访问 Azure Stack Edge 设备的客户端](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)中的 "导出证书" 中的步骤进行操作。


## <a name="validate-certificates"></a>验证证书

首先，将生成正确的文件夹结构，并将证书放入相应的文件夹中。 只需使用该工具验证证书。

1. 以管理员身份运行 PowerShell。

2. 若要生成适当的文件夹结构，请在提示符下键入：

    `New-AzsCertificateFolder -CertificateType AzureStackEdge -OutputPath "$ENV:USERPROFILE\Documents\AzureStackCSR"`

3. 将 PFX 密码转换为安全字符串。 键入：       

    `$pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString` 

4. 接下来，验证证书。 键入：

    `Invoke-AzsCertificateValidation -CertificateType AzureStackEdge -DeviceName mytea1 -NodeSerialNumber VM1500-00025 -externalFQDN azurestackedge.contoso.com -CertificatePath $ENV:USERPROFILE\Documents\AzureStackCSR\AzureStackEdge -pfxPassword $pfxPassword`

## <a name="next-steps"></a>后续步骤

[部署 Azure Stack Edge 设备](azure-stack-edge-gpu-deploy-prep.md)
