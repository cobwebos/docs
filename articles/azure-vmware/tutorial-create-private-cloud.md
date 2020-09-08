---
title: 教程 - 在 Azure 中部署 vSphere 群集
description: 了解如何使用 Azure VMWare 解决方案在 Azure 中部署 vSphere 群集
ms.topic: tutorial
ms.date: 09/07/2020
ms.openlocfilehash: 69a29a459ba283bb34169112ac2fa174ac6a14af
ms.sourcegitcommit: 8791f69d44150767807d215cafc4076f3ed43f9f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2020
ms.locfileid: "89512349"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud-in-azure"></a>教程：在 Azure 中部署 Azure VMware 解决方案私有云

使用 Azure VMware 解决方案，可以在 Azure 中部署 vSphere 群集。 最精简的初始部署是三个主机。 可以逐个添加更多的主机，每个群集最多可以包含 16 个主机。 

由于在启动时 Azure VMware 解决方案不允许使用本地 vCenter 管理私有云，因此需要对本地 vCenter 实例、虚拟网络等进行额外配置和连接。 本教程将介绍这些过程和相关先决条件。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建 Azure VMware 解决方案私有云
> * 验证已部署的私有云

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 拥有适当的管理权限和创建私有云的权限。
- 确保已按照以下文章中所述配置相应的网络：[教程：网络清单](tutorial-network-checklist.md)。

## <a name="register-the-resource-provider"></a>注册资源提供程序

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="create-a-private-cloud"></a>创建私有云

可以使用 [Azure 门户](#azure-portal)或 [Azure CLI](#azure-cli) 创建 Azure VMware 解决方案私有云。

### <a name="azure-portal"></a>Azure 门户

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-avs-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Azure CLI

可以通过 Azure Cloud Shell 来使用 Azure CLI，而不是使用 Azure 门户来创建 Azure VMware 解决方案私有云。 它是免费的交互式 Shell，预安装有常用 Azure 工具并将其配置为与帐户一起使用。 

#### <a name="open-azure-cloud-shell"></a>打开 Azure Cloud Shell

若要打开 Cloud Shell，请从代码块的右上角选择“试一试”。 也可以通过转到 [https://shell.azure.com/bash](https://shell.azure.com/bash) 在单独的浏览器标签页中启动 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后按 Enter 来运行它。

#### <a name="create-a-resource-group"></a>创建资源组

使用“[az group create](/cli/azure/group)”命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：

```
azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>创建私有云

提供资源组名称、私有云名称、位置和群集大小。

| 属性  | 说明  |
| --------- | ------------ |
| -g（资源组名称）     | 私有云资源的资源组的名称。        |
| -n（私有云名称）     | Azure VMware 解决方案私有云的名称。        |
| --location     | 用于私有云的位置。         |
| --cluster-size     | 群集的大小。 最小值为 3。         |
| --network-block     | 用于私有云的 CIDR IP 地址网络块。 地址块不应与订阅和本地网络中的其他虚拟网络中使用的地址块重叠。        |
| --sku | SKU 值：AV36 |

```
azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="delete-a-private-cloud-azure-portal"></a>删除私有云（Azure 门户）

如果你不再需要某个 Azure VMware 解决方案私有云，可将其删除。 删除私有云时，会一并删除所有群集及其所有组件。

为此，请在 Azure 门户中导航到你的私有云，然后选择“删除”。 在确认页上，输入私有云的名称以确认，然后选择“是”。

> [!CAUTION]
> 删除私有云是不可逆的操作。 删除私有云后，数据将无法恢复，因为此操作会终止所有正在运行的工作负载、组件，并销毁所有私有云数据和配置设置（包括公共 IP 地址）。 

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 创建 Azure VMware 解决方案私有云
> * 验证部署的私有云

请继续学习下一教程，了解如何在为私有云群集设置本地管理的过程中，创建一个用于私有云的虚拟网络。

> [!div class="nextstepaction"]
> [创建虚拟网络](tutorial-configure-networking.md)
