---
title: "使用 Azure 模板创建 Linux VM | Microsoft Docs"
description: "使用 Azure Resource Manager 模板在 Azure 上创建 Linux VM。"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 10/24/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: ea1274dd53a93f00fa251ed03684b17b58b009c2


---
# <a name="create-a-linux-vm-using-an-azure-template"></a>使用 Azure 模板创建 Linux VM
本文说明如何使用 Azure 模板在 Azure 上快速部署 Linux 虚拟机。  本文需要以下条件：

* 一个 Azure 帐户（[获取免费试用版](https://azure.microsoft.com/pricing/free-trial/)）。
* 已使用 `azure login` 登录 [Azure CLI](../xplat-cli-install.md)。
* Azure CLI *必须处于 *Azure Resource Manager 模式`azure config mode arm`。

也可以使用 [Azure 门户](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)快速部署 Linux VM 模板。

## <a name="quick-command-summary"></a>快速命令摘要
```azurecli
azure group create \
    -n myResourceGroup \
    -l westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## <a name="detailed-walkthrough"></a>详细演练
模板可让你在 Azure 上使用要在启动期间自定义的设置（如用户名和主机名设置）创建 VM。 对于本文，我们将利用 Ubuntu VM 和对 SSH 开放了端口 22 的网络安全组 (NSG) 启动 Azure 模板。

Azure Resource Manager 模板是可用于一次性简易任务（启动 Ubuntu VM）的 JSON 文件，如本文所述。  Azure 模板还可用于构建整个环境（如测试、开发或生产部署堆栈）的复杂 Azure 配置。

## <a name="create-the-linux-vm"></a>创建 Linux VM
下面的代码示例演示如何调用 `azure group create` 创建资源组，并同时使用[此 Azure Resource Manager 模板](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json)部署受 SSH 保护的 Linux VM。 请记住，在示例中必须使用环境中唯一的名称。 此示例使用 `myResourceGroup` 作为资源组名称，使用 `myVM` 作为 VM 名称。

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

该示例使用 `--template-uri` 参数部署了一台 VM。  还可以使用以模板文件路径作为参数的 `--template-file` 参数在本地下载或创建模板并传递模板。 Azure CLI 将提示你输入模板所需的参数。

## <a name="next-steps"></a>后续步骤
请搜索 [模板库](https://azure.microsoft.com/documentation/templates/) ，了解接下来要部署哪些应用框架。




<!--HONumber=Nov16_HO2-->


