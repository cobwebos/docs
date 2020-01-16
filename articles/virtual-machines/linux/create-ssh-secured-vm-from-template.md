---
title: 通过模板在 Azure 中创建 Linux VM
description: 如何使用 Azure CLI 基于资源管理器模板创建 Linux VM
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cf440542e8b16231514447ddcddca081b4b89368
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979053"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>如何使用 Azure 资源管理器模板创建 Linux 虚拟机

了解如何使用 Azure 资源管理器模板创建 Linux 虚拟机（VM）和 Azure Cloud shell 中的 Azure CLI。 若要创建 Windows 虚拟机，请参阅[从资源管理器模板创建 windows 虚拟机](../windows/ps-template.md)。

## <a name="templates-overview"></a>模板概述

Azure 资源管理器模板是 JSON 文件，其中定义了 Azure 解决方案的基础结构和配置。 使用模板，可以在解决方案的整个生命周期内重复部署该解决方案，确保以一致的状态部署资源。 若要详细了解模板的格式及其构造方法，请参阅[快速入门：使用 Azure 门户创建和部署 Azure 资源管理器模板](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)。 若要查看资源类型的 JSON 语法，请参阅[定义 Azure 资源管理器模板中的资源](/azure/templates/microsoft.compute/allversions)。

## <a name="create-a-virtual-machine"></a>创建虚拟机

创建 Azure 虚拟机通常包括两个步骤：

1. 创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 必须在创建虚拟机前创建资源组。
1. 创建虚拟机。

以下示例从[Azure 快速入门模板](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json)创建 VM。 此部署仅允许 SSH 身份验证。 出现提示时，提供自己的 SSH 公钥的值，例如 *~/.ssh/id_rsa.pub* 的内容。 如果需要创建 SSH 密钥对，请参阅[如何为 Azure 中的 Linux VM 创建和使用 SSH 密钥对](mac-create-ssh-keys.md)。 下面是该模板的副本：

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

若要运行 CLI 脚本，请选择 "**尝试**" 以打开 Azure Cloud shell。 若要粘贴脚本，请右键单击 shell，然后选择 "**粘贴**"：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the project name (used for generating resource names):" &&
read projectName &&
echo "Enter the administrator username:" &&
read username &&
echo "Enter the SSH public key:" &&
read key &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey="$key" &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

最后一个 Azure CLI 命令显示新创建的 VM 的公共 IP 地址。 需要公共 IP 地址才能连接到虚拟机。 请参阅本文的下一部分。

在前面的示例中，指定了 GitHub 中存储的一个模板。 还可以下载或创建模板并使用 `--template-file` 参数指定本地路径。

下面是一些其他资源：

- 若要了解如何开发资源管理器模板，请参阅 [Azure 资源管理器文档](/azure/azure-resource-manager/)。
- 若要查看 Azure 虚拟机架构，请参阅[azure 模板参考](/azure/templates/microsoft.compute/allversions)。
- 若要查看更多虚拟机模板示例，请参阅[Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)。

## <a name="connect-to-virtual-machine"></a>连接到虚拟机

然后，可以通过 SSH 照常连接到 VM。 在上述命令中提供自己的公共 IP 地址：

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>后续步骤

在此示例中，已创建了一个基本的 Linux VM。 对于包含应用程序框架或创建更复杂环境的更多资源管理器模板，请浏览[Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)。

若要了解有关创建模板的更多信息，请查看所部署的资源类型的 JSON 语法和属性：

- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
