---
title: "Bash in Azure Cloud Shell 功能 | Microsoft Docs"
description: "Bash in Azure Cloud Shell 的功能概述"
services: Azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: juluk
ms.openlocfilehash: 79c3f376c2800e2ce9123c31af6e1aa2aacfcf00
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2017
---
# <a name="features--tools-for-bash-in-azure-cloud-shell"></a>Bash in Azure Cloud Shell 的功能和工具

[!include [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> 此外还提供了 [PowerShell](features-powershell.md) 中的功能和工具。

Bash in Cloud Shell 在 `Ubuntu 16.04 LTS` 上运行。

## <a name="features"></a>功能

### <a name="secure-automatic-authentication"></a>安全自动身份验证

Bash in Cloud Shell 安全并自动地验证帐户对 Azure CLI 2.0 的访问权限。

### <a name="ssh-into-azure-linux-virtual-machines"></a>通过 SSH 连接到 Azure Linux 虚拟机

通过 Azure CLI 2.0 创建 Linux VM 可以创建默认的 SSH 密钥并将其放置在 `$Home` 目录中。 将 SSH 密钥放置在 `$Home` 中可以通过 Cloud Shell 与 Azure Linux VM 建立直接 SSH 连接。 密钥保存在文件共享中的 acc_<user>.img 内，使用文件共享或密钥或者共享其访问权限时，请遵循最佳做法。

### <a name="home-persistence-across-sessions"></a>$Home 可在不同的会话中持久保存

要在会话之间保存文件，Cloud Shell 会指导完成在首次启动时附加 Azure 文件共享。
完成后，Cloud Shell 会自动为所有将来的会话附加存储（装载为 `$Home\clouddrive`）。
此外，在 Bash in Cloud Shell 中，`$Home` 目录以 .img 格式持久保存在 Azure 文件共享中。
`$Home` 外部的文件和计算机状态不会持久保存在不同的会话中。

[详细了解如何在 Bash in Cloud Shell 中持久保存文件。](persisting-shell-storage.md)

## <a name="tools"></a>工具

|类别   |名称   |
|---|---|
|Linux 工具            |bash<br> sh<br> tmux<br> dig<br>               |
|Azure 工具            |[Azure CLI 2.0](https://github.com/Azure/azure-cli) 和 [1.0](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [批处理 Shipyard](https://github.com/Azure/batch-shipyard) <br> [Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) <br> [blobxfer](https://github.com/Azure/blobxfer#blobxfer) |
|文本编辑器           |vim<br> nano<br> emacs       |
|源代码管理         |git                    |
|生成工具            |make<br> maven<br> npm<br> pip         |
|容器             |[Docker CLI](https://github.com/docker/cli)/[Docker 计算机](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [DC/OS CLI](https://github.com/dcos/dcos-cli)         |
|数据库              |MySQL 客户端<br> PostgreSql 客户端<br> [sqlcmd 实用工具](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|其他                  |iPython 客户端<br> [Cloud Foundry CLI](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)|

## <a name="language-support"></a>语言支持

|语言   |版本   |
|---|---|
|.NET       |2.0.0       |
|Go         |1.7        |
|Java       |1.8        |
|Node.js    |6.9.4      |
|PowerShell |[6.0 (beta)](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 和 3.5（默认）|

## <a name="next-steps"></a>后续步骤
[Bash in Cloud Shell 快速入门](quickstart.md) <br>
[了解 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/)