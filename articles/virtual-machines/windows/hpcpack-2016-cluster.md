---
title: "Azure 中的 HPC Pack 2016 群集 | Microsoft Docs"
description: "了解如何在 Azure 中部署 HPC Pack 2016 群集"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3dde6a68-e4a6-4054-8b67-d6a90fdc5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 12/15/2016
ms.author: danlep
ms.openlocfilehash: 88d1f4e29f38ba1a6bef57c2da43bee205575eee
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="deploy-an-hpc-pack-2016-cluster-in-azure"></a>在 Azure 中部署 HPC Pack 2016 群集

按照本文中的步骤操作，以在 Azure 虚拟机中部署 [Microsoft HPC Pack 2016](https://technet.microsoft.com/library/cc514029) 群集。 HPC Pack 是在 Microsoft Azure 和 Windows Server 技术基础之上构建的 Microsoft 免费 HPC 解决方案，支持各种 HPC 工作负荷。

使用其中一个 [Azure Resource Manager 模板](https://github.com/MsHpcPack/HPCPack2016)部署 HPC Pack 2016 群集。 可以选择多个群集拓扑，其中群集头节点数目可以不同，也可以含有 Linux 或 Windows 计算节点。

## <a name="prerequisites"></a>先决条件

### <a name="pfx-certificate"></a>PFX 证书

Microsoft HPC Pack 2016 群集需要用于保护 HPC 节点之间通信的个人信息交换 (PFX) 证书。 该证书必须满足以下要求：

* 它必须具有可以进行密钥交换的私钥
* 密钥使用包括数字签名和密钥加密
* 增强型密钥使用包括客户端身份验证和服务器身份验证

如果不具有满足这些要求的证书，可向证书颁发机构请求获取证书。 或者，也可以基于运行该命令的操作系统，使用以下命令生成自签名证书，并使用私钥导出 PFX 格式的证书。

* **对于 Windows 10 或 Windows Server 2016**，运行内置的 **New-selfsignedcertificate** PowerShell cmdlet，如下所示：

  ```PowerShell
  New-SelfSignedCertificate -Subject "CN=HPC Pack 2016 Communication" -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2") -CertStoreLocation cert:\CurrentUser\My -KeyExportPolicy Exportable -NotAfter (Get-Date).AddYears(5)
  ```
* **对于早于 Windows 10 或 Windows Server 2016 的操作系统**，需要从 Microsoft 脚本中心下载[自签名证书生成器](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/)。 提取其内容并在 PowerShell 提示符处运行以下命令：

    ```PowerShell 
    Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
  
    New-SelfSignedCertificateEx -Subject "CN=HPC Pack 2016 Communication" -KeySpec Exchange -KeyUsage "DigitalSignature,KeyEncipherment" -EnhancedKeyUsage "Server Authentication","Client Authentication" -StoreLocation CurrentUser -Exportable -NotAfter (Get-Date).AddYears(5)
    ```

### <a name="upload-certificate-to-an-azure-key-vault"></a>将证书上传到 Azure 密钥保管库

在部署 HPC 群集之前，将证书上传到 [Azure 密钥保管库](../../key-vault/index.md)作为密钥，并记录部署期间使用的以下信息：“保管库名称”、“保管库资源组”、“证书 URL”和“证书指纹”。

以下是用于上传证书的 PowerShell 脚本示例。 有关将证书上传到 Azure 密钥保管库的详细信息，请参阅 [Azure 密钥保管库入门](../../key-vault/key-vault-get-started.md)。

```powershell
#Give the following values
$VaultName = "mytestvault"
$SecretName = "hpcpfxcert"
$VaultRG = "myresourcegroup"
$location = "westus"
$PfxFile = "c:\Temp\mytest.pfx"
$Password = "yourpfxkeyprotectionpassword"
#Validate the pfx file
try {
    $pfxCert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $PfxFile, $Password
}
catch [System.Management.Automation.MethodInvocationException]
{
    throw $_.Exception.InnerException
}
$thumbprint = $pfxCert.Thumbprint
$pfxCert.Dispose()
# Create and encode the JSON object
$pfxContentBytes = Get-Content $PfxFile -Encoding Byte
$pfxContentEncoded = [System.Convert]::ToBase64String($pfxContentBytes)
$jsonObject = @"
{
"data": "$pfxContentEncoded",
"dataType": "pfx",
"password": "$Password"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
#Create an Azure key vault and upload the certificate as a secret
$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
$rg = Get-AzureRmResourceGroup -Name $VaultRG -Location $location -ErrorAction SilentlyContinue
if($null -eq $rg)
{
    $rg = New-AzureRmResourceGroup -Name $VaultRG -Location $location
}
$hpcKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultRG -Location $location -EnabledForDeployment -EnabledForTemplateDeployment
$hpcSecret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secret
"The following Information will be used in the deployment template"
"Vault Name             :   $VaultName"
"Vault Resource Group   :   $VaultRG"
"Certificate URL        :   $($hpcSecret.Id)"
"Certificate Thumbprint :   $thumbprint"

```


## <a name="supported-topologies"></a>支持的拓扑

选择其中一个 [Azure Resource Manager 模板](https://github.com/MsHpcPack/HPCPack2016)部署 HPC Pack 2016 群集。 以下是三个受支持群集拓扑的高级体系结构。 高可用性拓扑包括多个群集头节点。

1. 包含 Active Directory 域的高可用性群集

    ![AD 域中的 HA 群集](./media/hpcpack-2016-cluster/haad.png)



2. 不包含 Active Directory 域的高可用性群集

    ![不包含 AD 域的 HA 群集](./media/hpcpack-2016-cluster/hanoad.png)

3. 含有一个头节点的群集

   ![含有一个头节点的群集](./media/hpcpack-2016-cluster/singlehn.png)


## <a name="deploy-a-cluster"></a>部署群集

要创建群集，请选择一个模板，并单击“部署到 Azure”。 在 [Azure 门户](https://portal.azure.com)中，指定该模板的参数，如以下步骤中所示。 每个模板为 HPC 群集基础结构创建所需的全部 Azure 资源。 资源包括 Azure 虚拟网络、公共 IP 地址、负载均衡器（仅适用于高可用性群集）、网络接口、可用性集、存储帐户和虚拟机。

### <a name="step-1-select-the-subscription-location-and-resource-group"></a>步骤 1：选择订阅、位置和资源组

**订阅**和**位置**必须相同，指定 PFX 证书的上载时 （请参阅先决条件）。 我们建议为部署创建“资源组”。

### <a name="step-2-specify-the-parameter-settings"></a>步骤 2：指定参数设置

输入或修改模板参数的值。 单击每个参数旁边的图标可获得帮助信息。 另请参阅 [VM 可用大小](sizes.md)准则。

指定以下参数的先决条件中记录的值：**保管库名称**，**保管库资源组**，**证书 URL**，和**证书指纹**。

### <a name="step-3-review-legal-terms-and-create"></a>步骤 3. 查看法律条款和创建
单击“查看法律条款”，查看条款。 如果同意，单击“购买”，并单击“创建”以开始部署。

## <a name="connect-to-the-cluster"></a>连接至群集
1. HPC Pack 群集完成部署后，请转到 [Azure 门户](https://portal.azure.com)。 单击“资源组”，并查找已部署群集的资源组。 可找到头节点虚拟机。

    ![门户中的群集头节点](./media/hpcpack-2016-cluster/clusterhns.png)

2. 单击一个头节点（在高可用性群集中，单击任何一个头节点）。 在 **Essentials** 中，可以找到群集的公共 IP 地址或完整的 DNS 名称。

    ![群集连接设置](./media/hpcpack-2016-cluster/clusterconnect.png)

3. 单击“连接”，以使用指定的管理员用户名通过远程桌面登录到任何一个头节点。 如果已部署的群集位于 Active Directory 域中，用户名的格式为 <privateDomainName>\<adminUsername>（例如，hpc.local\hpcadmin）。

## <a name="next-steps"></a>后续步骤
* 向群集提交作业。 请参阅[在 Azure 中将作业提交到 HPC（即 HPC Pack 群集）](hpcpack-cluster-submit-jobs.md)和[使用 Azure Active Directory 在 Azure 中管理 HPC Pack 2016 群集](hpcpack-cluster-active-directory.md)。

