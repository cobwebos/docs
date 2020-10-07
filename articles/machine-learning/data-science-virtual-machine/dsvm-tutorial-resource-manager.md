---
title: 快速入门：创建 Data Science VM - 资源管理器模板
titleSuffix: Azure Data Science Virtual Machine
description: 在本快速入门中，你将使用 Azure 资源管理器模板快速部署 Data Science Virtual Machine
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 06/10/2020
ms.service: machine-learning
ms.subservice: data-science-vm
ms.topic: quickstart
ms.openlocfilehash: 8f83cdb09b94fb7eda1f8f700390be8b2c2147e0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "90883150"
---
# <a name="quickstart-create-an-ubuntu-data-science-virtual-machine-using-an-arm-template"></a>快速入门：使用 ARM 模板创建 Ubuntu Data Science Virtual Machine

本快速入门将介绍如何使用 Azure 资源管理器模板（ARM 模板）创建 Ubuntu 18.04 Data Science Virtual Machine。 Data Science Virtual Machine 是基于云的虚拟机，预加载了一套数据科学和机器学习框架及工具。 当部署在 GPU 驱动的计算资源上时，所有工具和库都配置为使用 GPU。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-ubuntu-DSVM-GPU-or-CPU%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/services/machine-learning/)。

* 若要从本地环境使用本文档中的 CLI 命令，需要使用 [Azure CLI](/cli/azure/install-azure-cli)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-vm-ubuntu-DSVM-GPU-or-CPU/)。

:::code language="json" source="~/quickstart-templates/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json":::

该模板中定义了以下资源：

* [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
* [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)：创建基于云的虚拟机。 在此模板中，将虚拟机配置为运行 Ubuntu 18.04 的 Data Science Virtual Machine。

## <a name="deploy-the-template"></a>部署模板

若要通过 Azure CLI 使用该模板，请登录并选择你的订阅（请参阅[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli)）。 然后运行：

```azurecli-interactive
read -p "Enter the name of the resource group to create:" resourceGroupName &&
read -p "Enter the Azure location (e.g., centralus):" location &&
read -p "Enter the authentication type (must be 'password' or 'sshPublicKey') :" authenticationType &&
read -p "Enter the login name for the administrator account (may not be 'admin'):" adminUsername &&
read -p "Enter administrator account secure string (value of password or ssh public key):" adminPasswordOrKey &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters adminUsername=$adminUsername authenticationType=$authenticationType adminPasswordOrKey=$adminPasswordOrKey &&
echo "Press [ENTER] to continue ..." &&
read
```

运行上述命令时，请输入：

1. 要创建的包含 DSVM 和关联资源的资源组的名称。
1. 要在其中进行部署的 Azure 位置。
1. 要使用的身份验证类型（输入字符串 `password` 或 `sshPublicKey`）。
1. 管理员帐户的登录名（此值不能为 `admin`）。
1. 帐户的密码或 SSH 公钥的值。

## <a name="review-deployed-resources"></a>查看已部署的资源

若要查看 Data Science Virtual Machine：

1. 转到 [Azure 门户](https://portal.azure.com)
1. 登录。
1. 选择你刚才创建的资源组。

你将看到资源组的信息：

:::image type="content" source="media/dsvm-tutorial-resource-manager/resource-group-home.png" alt-text="包含 DSVM 的基本资源组的屏幕截图":::

单击虚拟机资源转到其信息页面。 可在此处找到 VM 的相关信息，包括连接详细信息。

## <a name="clean-up-resources"></a>清理资源

如果不想使用此虚拟机，请删除它。 由于 DSVM 与其他资源（例如存储帐户）关联，因此可能需要删除所创建的整个资源组。 可以使用门户删除资源组，方法是单击“删除”按钮并进行确认。 也可以从 CLI 中使用以下命令删除资源组：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已通过 ARM 模板创建了 Data Science Virtual Machine。

> [!div class="nextstepaction"]
> [示例程序和 ML 演练](dsvm-samples-and-walkthroughs.md)
