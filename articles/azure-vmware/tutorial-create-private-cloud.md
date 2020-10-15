---
title: 教程 - 在 Azure 中部署 vSphere 群集
description: 了解如何使用 Azure VMWare 解决方案在 Azure 中部署 vSphere 群集
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: b673a67fac734c9cb63e96b6a0c3dc9182f4b994
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91952278"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud-in-azure"></a>教程：在 Azure 中部署 Azure VMware 解决方案私有云

使用 Azure VMware 解决方案，可以在 Azure 中部署 vSphere 群集。 最精简的初始部署是三个主机。 可以逐个添加更多的主机，每个群集最多可以包含 16 个主机。 

由于在启动时 Azure VMware 解决方案不允许使用本地 vCenter 管理私有云，因此需要进行额外配置。 本教程将介绍这些过程和相关先决条件。

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

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Azure CLI

可以通过 Azure Cloud Shell 来使用 Azure CLI，而不是使用 Azure 门户来创建 Azure VMware 解决方案私有云。  有关可与 Azure VMware 解决方案一起使用的命令的列表，请参阅 [Azure VMware 命令](/cli/azure/ext/vmware/vmware)。

#### <a name="open-azure-cloud-shell"></a>打开 Azure Cloud Shell

从代码块的右上角选择“试用”。 也可以通过转到 [https://shell.azure.com/bash](https://shell.azure.com/bash) 在单独的浏览器标签页中启动 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后按 Enter 来运行它。

#### <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：

```azurecli-interactive

az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>创建私有云

提供资源组和私有云的名称、位置和群集大小。

| 属性  | 说明  |
| --------- | ------------ |
| -g（资源组名称）     | 私有云资源的资源组的名称。        |
| -n（私有云名称）     | Azure VMware 解决方案私有云的名称。        |
| --location     | 用于私有云的位置。         |
| --cluster-size     | 群集的大小。 最小值为 3。         |
| --network-block     | 用于私有云的 CIDR IP 地址网络块。 地址块不应与订阅和本地网络中的其他虚拟网络中使用的地址块重叠。        |
| --sku | SKU 值：AV36 |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="delete-an-azure-vmware-solution-private-cloud"></a>删除 Azure VMware 解决方案私有云

如果你不再需要某个 Azure VMware 解决方案私有云，可将其删除。 Azure VMware 解决方案私有云包括隔离网络域、在专用服务器节点上预配的一个或多个 vSphere 群集，通常还有很多虚拟机。 删除私有云时，将删除所有虚拟机、其数据和群集。 专用裸机节点会被安全地擦除并返回到可用池。 为客户预配的网域会被删除。  

> [!CAUTION]
> 删除私有云是不可逆的操作。 删除私有云后，数据将无法恢复，因为此操作会终止所有正在运行的工作负载和组件，并销毁所有私有云数据和配置设置（包括公共 IP 地址）。

### <a name="prerequisites"></a>必备知识

删除私有云后，将无法恢复虚拟机及其数据。 如果之后需要虚拟机数据，管理员必须先备份所有数据，然后再删除私有云。

### <a name="steps-to-delete-an-azure-vmware-solution-private-cloud"></a>删除 Azure VMware 解决方案私有云的步骤

1. 访问 Azure 门户中的“Azure VMware 解决方案”页。

2. 选择要删除的私有云。
 
3. 输入私有云的名称，并选择“是”。 几小时后，删除过程就会完成。  

## <a name="azure-vmware-commands"></a>Azure VMware 命令

有关可与 Azure VMware 解决方案一起使用的命令的列表，请参阅 [Azure VMware 命令](/cli/azure/ext/vmware/vmware)。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 创建 Azure VMware 解决方案私有云
> * 验证已部署的私有云
> * 删除 Azure VMware 解决方案私有云

继续学习下一教程，了解如何创建跳转盒。 使用跳转盒连接到你的环境，以便在本地管理你的私有云。


> [!div class="nextstepaction"]
> [访问 Azure VMware 解决方案私有云](tutorial-access-private-cloud.md)