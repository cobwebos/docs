---
title: 将 Azure 模板与 Azure CLI 1.0 配合使用创建 Linux VM | Microsoft Docs
description: 在 Azure 上使用 Azure CLI 1.0 和 Azure 资源管理器模板创建 Linux VM。
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: v-livech
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 620adcba9e136aa56e7a4867196ce5b9249b6072
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-create-a-linux-vm-using-the-azure-cli-10-an-azure-resource-manager-template"></a>如何使用 Azure CLI 1.0 和 Azure 资源管理器模板创建 Linux VM
本文说明如何使用 Azure CLI 1.0 和 Azure 资源管理器模板快速部署 Linux 虚拟机。 本文需要以下条件：

* 一个 Azure 帐户（[获取免费试用版](https://azure.microsoft.com/pricing/free-trial/)）。
* 已使用 `azure login` 登录 [Azure CLI 1.0](../../cli-install-nodejs.md)。
* Azure CLI *必须处于* Azure 资源管理器模式`azure config mode arm`。

也可以使用 [Azure 门户](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)快速部署 Linux VM 模板。

## <a name="cli-versions-to-complete-the-task"></a>用于完成任务的 CLI 版本
可以使用以下 CLI 版本之一完成任务：

- [Azure CLI 1.0](#quick-command-summary) – 用于经典部署模型和资源管理部署模型（本文）的 CLI
- [Azure CLI 2.0](create-ssh-secured-vm-from-template.md) - 适用于资源管理部署模型的下一代 CLI

## <a name="quick-command-summary"></a>快速命令摘要
```azurecli
azure group create \
    -n myResourceGroup \
    -l westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## <a name="detailed-walkthrough"></a>详细演练
借助模板，可以在 Azure 上使用要在启动期间自定义的设置（如用户名和主机名设置）创建 VM。 对于本文，我们将利用 Ubuntu VM 和对 SSH 开放了端口 22 的网络安全组 (NSG) 启动 Azure 模板。

Azure 资源管理器模板是可用于一次性简易任务（启动 Ubuntu VM）的 JSON 文件，如本文所述。  Azure 模板还可用于构建整个环境（如测试、开发或生产部署堆栈）的复杂 Azure 配置。

## <a name="create-the-linux-vm"></a>创建 Linux VM
下面的代码示例演示如何调用 `azure group create` 创建资源组，并同时使用[此 Azure 资源管理器模板](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json)部署受 SSH 保护的 Linux VM。 请记住，在示例中必须使用环境中唯一的名称。 此示例使用 *myResourceGroup* 作为资源组名称，使用 *myVM* 作为 VM 名称。

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

输出应类似于以下输出块：

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== myAdminUser@myVM
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

该示例使用 `--template-uri` 参数部署了一台 VM。  还可以使用以模板文件路径作为参数的 `--template-file` 参数在本地下载或创建模板并传递模板。 Azure CLI 会提示输入模板所需的参数。

## <a name="next-steps"></a>后续步骤
请搜索 [模板库](https://azure.microsoft.com/documentation/templates/) ，了解接下来要部署哪些应用框架。

