---
title: "为 Azure 堆栈用户启用 Azure CLI |Microsoft 文档"
description: "了解如何使用跨平台命令行界面 (CLI) 来管理和部署 Azure 堆栈上的资源"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: e2483bda5a0c6a6b270759946f146c37c5dad5b1
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>为 Azure 堆栈用户启用 Azure CLI

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

你可以通过使用 Azure CLI 执行任何 Azure 堆栈特定运算符的任务不存在。 然而，用户可以管理通过 CLI 资源之前，Azure 堆栈运算符必须提供它们替换为以下内容：

* **Azure 堆栈 CA 根证书**是必需的如果用户在从外部 Azure 堆栈开发工具包工作站使用 CLI。  

* **虚拟机别名终结点**提供别名，如"UbuntuLTS"或"Win2012Datacenter"部署 Vm 时为单个参数引用映像发布者、 产品/服务、 SKU、 和版本。  

以下各节描述了如何获取这些值。

## <a name="export-the-azure-stack-ca-root-certificate"></a>Azure 堆栈 CA 根证书导出

Azure 堆栈 CA 根证书是在开发工具包，在开发工具包环境内运行的租户虚拟机上可用。 若要导出 PEM 格式中的 Azure 堆栈根证书，在登录到你的开发工具包或租户虚拟机并运行以下脚本：

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Log-Error "Certificate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>设置虚拟机别名终结点

Azure 堆栈运算符应设置可公开访问的终结点承载虚拟机别名文件。 虚拟机别名文件是一个 JSON 文件，提供的映像的公用名。 虚拟机部署为 Azure CLI 参数时，随后指定该名称。  

将条目添加到别名文件之前，请确保你[从 Azure 应用商店下载映像](azure-stack-download-azure-marketplace-item.md)，或具有[发布你自己的自定义映像](azure-stack-add-vm-image.md)。 如果发布的自定义映像，请记下发布过程中指定的发布者、 产品/服务、 SKU、 和版本信息。 如果它是从应用商店的映像，你可以通过查看信息```Get-AzureVMImage```cmdlet。  
   
A[示例别名文件](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json)别名可与许多常见的映像。 你可以将其用作起始点。 将此文件放在你的 CLI 客户端可以访问的空间中。 一种方法是托管 blob 存储帐户中的文件并与用户共享 URL:

1. 下载[示例文件](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json)从 GitHub。
2. 在 Azure 堆栈中创建新的存储帐户。 完成该操作时，创建新的 blob 容器。 设置访问策略为"public"。  
3. 将 JSON 文件上载到新的容器。 当完成该操作时，你可以通过单击 blob 名称，然后选择从 blob 属性的 URL 查看 blob 的 URL。


## <a name="next-steps"></a>后续步骤

[使用 Azure CLI 部署模板](azure-stack-deploy-template-command-line.md)

[使用 PowerShell 连接](azure-stack-connect-powershell.md)

[管理用户权限](azure-stack-manage-permissions.md)

