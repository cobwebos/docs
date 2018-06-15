---
title: Bash in Azure Cloud Shell 快速入门 | Microsoft Docs
description: Cloud Shell 中的 Bash 快速入门
services: ''
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: juluk
ms.openlocfilehash: 4b7e4302bba2efed12e19043da1f592bed12a2fd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608876"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Azure Cloud Shell 中的 Bash 快速入门

本文档详细介绍如何在 [Azure 门户](https://ms.portal.azure.com/)中使用 Azure Cloud Shell 中的 Bash。

> [!NOTE]
> 此外，还提供了 [Azure Cloud Shell 中的 PowerShell](quickstart-powershell.md) 快速入门。

## <a name="start-cloud-shell"></a>启动 Cloud Shell
1. 从 Azure 门户的顶部导航栏启动 **Cloud Shell**。 <br>
![](media/quickstart/shell-icon.png)

2. 选择用于创建存储帐户和 Microsoft Azure 文件共享的订阅。
3. 选择“创建存储”

> [!TIP]
> 在每个会话中会自动针对 Azure CLI 2.0 对你进行身份验证。

### <a name="select-the-bash-environment"></a>选择 Bash 环境
检查 shell 窗口左侧的环境下拉列表是否显示 `Bash`。 <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>设置订阅
1. 列出可以访问的订阅。
```azurecli-interactive
az account list
```

2. 设置首选订阅： <br>
```azurecli-interactive
az account set --subscription my-subscription-name`
```

> [!TIP]
> 将使用 `/home/<user>/.azure/azureProfile.json` 为将来的会话记住订阅。

### <a name="create-a-resource-group"></a>创建资源组
在 WestUS 中创建一个名为“MyRG”的新资源组。
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>创建 Linux VM
在新资源组中创建 Ubuntu VM。 Azure CLI 2.0 将创建 SSH 密钥并使用它们设置 VM。 <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> 使用 `--generate-ssh-keys` 指示 Azure CLI 2.0 在 VM 和 `$Home` 目录中创建并设置公钥和私钥。 默认情况下，密钥放置在 Cloud Shell 中的 `/home/<user>/.ssh/id_rsa` 和 `/home/<user>/.ssh/id_rsa.pub`。 `.ssh` 文件夹保存在附加的文件共享的 5 GB 映像（用于持久保存 `$Home`）中。

此 VM 中的用户名将是你在 Cloud Shell 中使用的用户名 ($User@Azure:)。

### <a name="ssh-into-your-linux-vm"></a>通过 SSH 连接到 Linux VM
1. 在 Azure 门户搜索栏中搜索 VM 名称。
2. 单击“连接”以获取 VM 名称和公共 IP 地址。 <br>
![](media/quickstart/sshcmd-copy.png)

3. 使用 `ssh` 命令通过 SSH 连接到 VM。
```
ssh username@ipaddress
```

建立 SSH 连接后，应看到 Ubuntu 欢迎提示。 <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>清理 
1. 退出 SSH 会话。
```azurecli-interactive
exit
```

2. 删除资源组及其中的任何资源。
```azurecli-interactive
az group delete -n MyRG
```

## <a name="next-steps"></a>后续步骤
[了解如何为 Cloud Shell 中的 Bash 持久保存文件](persisting-shell-storage.md) <br>
[了解 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/) <br>
[了解 Azure 文件存储](../storage/files/storage-files-introduction.md) <br>
