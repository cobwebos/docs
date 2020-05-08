---
title: Azure Cloud Shell 功能 | Microsoft Docs
description: Azure Cloud Shell 中的功能概述
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: damaerte
ms.openlocfilehash: 7490eef7b6203b6bb2623efa4518c7bee5400fb3
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610762"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Azure Cloud Shell 的功能和工具

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell 在 `Ubuntu 16.04 LTS` 上运行。

## <a name="features"></a>功能

### <a name="secure-automatic-authentication"></a>安全自动身份验证

Cloud Shell 安全、自动地验证帐户对 Azure CLI 和 Azure PowerShell 的访问权限。

### <a name="home-persistence-across-sessions"></a>$HOME 可在会话之间持久保存

要在会话之间保存文件，Cloud Shell 会指导完成在首次启动时附加 Azure 文件共享。
完成后，Cloud Shell 会自动为所有将来的会话附加存储（装载为 `$HOME\clouddrive`）。
此外，`$HOME` 目录以 .img 格式持久保存在 Azure 文件共享中。
`$HOME` 外部的文件和计算机状态不会持久保存在不同的会话中。 存储机密（如 SSH 密钥）时，请使用最佳做法。 [像 Azure Key Vault 这样的服务有设置教程](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites)。

[详细了解如何在 Cloud Shell 中持久保存文件。](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Azure 驱动器 (Azure:)

PowerShell in Cloud Shell 提供 Azure 驱动器（`Azure:`）。 可以通过切换到的 Azure 驱动器`cd Azure:`并返回到主目录。 `cd  ~`
使用 Azure 驱动器可以轻松发现和浏览 Azure 资源（例如计算、网络、存储等），类似于文件系统导航。
无论位于何种驱动器，都可以继续使用熟悉的 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure) 来管理这些资源。
直接在 Azure 门户中或者通过 Azure PowerShell cmdlet 对 Azure 资源所做的任何更改会反映在 Azure 驱动器中。  可以运行 `dir -Force` 刷新资源。

![](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>管理 Exchange Online

Cloud Shell 中的 PowerShell 包含 Exchange Online 模块的专用生成。  运行`Connect-EXOPSSession`以获取 Exchange cmdlet。

![](media/features-powershell/exchangeonline.png)

 `Get-Command -Module tmp_*`运行
> [!NOTE]
> 模块名称应以开头`tmp_`，如果已使用相同的前缀安装了模块，则还会显示其 cmdlet。 

![](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>与开源工具深度集成

Cloud Shell 预先配置了开放源代码工具（如 Terraform、Ansible 和 Chef InSpec）的身份验证。 请通过示例演练试用该功能。

## <a name="tools"></a>工具

|类别   |名称   |
|---|---|
|Linux 工具            |bash<br> zsh<br> sh<br> tmux<br> dig<br>               |
|Azure 工具            |[Azure CLI](https://github.com/Azure/azure-cli) 和 [Azure 经典 CLI](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)<br> [Azure Functions CLI](https://github.com/Azure/azure-functions-core-tools)<br> [Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|文本编辑器           |代码（Cloud Shell 编辑器）<br> vim<br> nano<br> emacs    |
|源代码管理         |git                    |
|生成工具            |make<br> maven<br> npm<br> pip         |
|容器             |[Docker 计算机](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [DC/OS CLI](https://github.com/dcos/dcos-cli)         |
|数据库              |MySQL 客户端<br> PostgreSql 客户端<br> [sqlcmd 实用工具](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|其他                  |iPython 客户端<br> [Cloud Foundry CLI](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef InSpec](https://www.chef.io/inspec/)<br> [Puppet 螺栓](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp Packer](https://www.packer.io/)|

## <a name="language-support"></a>语言支持

|语言   |Version   |
|---|---|
|.NET Core  |2.2.402       |
|Go         |1.9        |
|Java       |1.8        |
|Node.js    |8.16.0      |
|PowerShell |[版](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 和 3.5（默认）|

## <a name="next-steps"></a>后续步骤
[Cloud Shell 中的 Bash 快速入门](quickstart.md) <br>
[Cloud Shell 快速入门中的 PowerShell](quickstart-powershell.md) <br>
[了解 Azure CLI](https://docs.microsoft.com/cli/azure/) <br>
[了解 Azure PowerShell](https://docs.microsoft.com/powershell/azure/) <br>
