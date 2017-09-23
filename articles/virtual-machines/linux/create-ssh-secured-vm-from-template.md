---
title: "基于模板在 Azure 中创建 Linux VM | Microsoft Docs"
description: "如何使用 Azure CLI 2.0 基于 Resource Manager 模板创建 Linux VM"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/12/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 908a8a0c82b2d21fb25c9b33dbd714570d1ac272
ms.contentlocale: zh-cn
ms.lasthandoff: 05/15/2017

---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>如何使用 Azure Resource Manager 模板创建 Linux 虚拟机
本文介绍了如何使用 Azure Resource Manager 模板和 Azure CLI 2.0 快速部署 Linux 虚拟机 (VM)。 还可以使用 [Azure CLI 1.0](create-ssh-secured-vm-from-template-nodejs.md) 执行这些步骤。


## <a name="templates-overview"></a>模板概述
Azure Resource Manager 模板是 JSON 文件，其中定义了 Azure 解决方案的基础结构和配置。 使用模板，可以在解决方案的整个生命周期内重复部署该解决方案，确保以一致的状态部署资源。 若要详细了解模板的格式及其构造方法，请参阅[创建你的第一个 Azure Resource Manager 模板](../../azure-resource-manager/resource-manager-create-first-template.md)。 若要查看资源类型的 JSON 语法，请参阅[定义 Azure Resource Manager 模板中的资源](/azure/templates/)。


## <a name="create-resource-group"></a>创建资源组
Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 必须在创建虚拟机前创建资源组。 以下示例在 *eastus* 区域中创建名为 *myResourceGroupVM* 的资源组：

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>创建虚拟机
以下示例通过 [az group deployment create](/cli/azure/group/deployment#create)基于[此 Azure Resource Manager 模板](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json)创建 VM。 提供你自己的 SSH 公钥的值，例如 *~/.ssh/id_rsa.pub* 的内容。 如果需要创建 SSH 密钥对，请参阅[如何为 Azure 中的 Linux VM 创建和使用 SSH 密钥对](mac-create-ssh-keys.md)。

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
  --parameters '{"sshKeyData": {"value": "ssh-rsa AAAAB3N{snip}B9eIgoZ"}}'
```

在此示例中，你指定了 GitHub 中存储的一个模板。 你还可以下载或创建模板并使用同一 `--template-file` 参数指定本地路径。

若要通过 SSH 连接到 VM，请使用 [az network public-ip show](/cli/azure/network/public-ip#show) 获取公共 IP 地址：

```azurecli
az network public-ip show \
    --resource-group myResourceGroup \
    --name sshPublicIP \
    --query [ipAddress] \
    --output tsv
```

然后，可以通过 SSH 照常连接到 VM：

```bash
ssh azureuser@<ipAddress>
```

## <a name="next-steps"></a>后续步骤
在此示例中，你已创建了一个基本的 Linux VM。 若要获取包括应用程序框架或创建更复杂环境的 Resource Manager 模板，请浏览 [Azure 快速入门模板库](https://azure.microsoft.com/documentation/templates/)。
