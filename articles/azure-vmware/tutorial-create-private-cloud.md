---
title: 教程 - 在 Azure 中部署 vSphere 群集
description: 了解如何使用 Azure VMWare 解决方案 (AVS) 在 Azure 中部署 vSphere 群集
ms.topic: tutorial
ms.date: 07/15/2020
ms.openlocfilehash: 4f3b33ea401c62124ae5f8a4c881d86d2f19b40c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079411"
---
# <a name="tutorial-deploy-an-avs-private-cloud-in-azure"></a>教程：在 Azure 中部署 AVS 私有云

使用 Azure VMware 解决方案 (AVS)，可以在 Azure 中部署 vSphere 群集。 最精简的初始部署是三个主机。 可以逐个添加更多的主机，每个群集最多可以包含 16 个主机。 

由于在启动时 AVS 不允许使用本地 vCenter 管理私有云，因此需要对本地 vCenter 实例、虚拟网络等进行额外配置和连接。 本教程将介绍这些过程和相关先决条件。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建 AVS 私有云
> * 验证已部署的私有云

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 拥有适当的管理权限和创建私有云的权限。
- 确保已按照以下文章中所述配置相应的网络：[教程：网络清单](tutorial-network-checklist.md)。

## <a name="register-the-resource-provider"></a>注册资源提供程序

若要使用 AVS，必须首先将资源提供程序注册到你的订阅。

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

有关注册资源提供程序的其他方式，请参阅 [Azure 资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md)。


## <a name="create-a-private-cloud"></a>创建私有云

可以使用 [Azure 门户](#azure-portal)或 [Azure CLI](#azure-cli) 创建 AVS 私有云。

### <a name="azure-portal"></a>Azure 门户

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 选择“创建新资源”。 在“搜索市场”文本框中键入 `Azure VMware Solution`，然后从列表中选择“Azure VMware 解决方案” 。 在“Azure VMware 解决方案”窗口中选择“创建”

1. 在“基本信息”选项卡上，输入相关字段的值。 下表列出了字段的属性。

   | 字段   | 值  |
   | ---| --- |
   | **订阅** | 你打算用于部署的订阅。|
   | **资源组** | 私有云资源的资源组。 |
   | **位置** | 选择一个位置，例如“美国东部”。|
   | **资源名称** | AVS 私有云的名称。 |
   | **SKU** | 选择以下 SKU 值：AV36 |
   | **主机** | 要添加到私有云群集的主机数。 默认值为 3，在部署后可以增大或减小此值。  |
   | **vCenter 管理员密码** | 输入云管理员密码。 |
   | **NSX-T 管理者密码** | 输入 NSX-T 管理员密码。 |
   | **地址块** | 输入私有云 CIDR 网络的 IP 地址块,，例如 10.175.0.0/22。 |

   :::image type="content" source="./media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="创建私有云" border="true":::

1. 完成后，选择“审阅 + 创建”。 在下一个屏幕上，验证输入的信息。 如果信息全部正确，请选择“创建”。

   > [!NOTE]
   > 此步骤大约需要两小时。 

1. 验证部署是否成功。 导航到创建的资源组，然后选择私有云。  完成部署后，你将看到“成功”状态。 

   :::image type="content" source="./media/tutorial-create-private-cloud/validate-deployment.png" alt-text="验证部署的私有云" border="true":::

### <a name="azure-cli"></a>Azure CLI

可以通过 Azure Cloud Shell 来使用 Azure CLI，而不是使用 Azure 门户来创建 AVS 私有云。 它是免费的交互式 Shell，预安装有常用 Azure 工具并将其配置为与帐户一起使用。 

#### <a name="open-azure-cloud-shell"></a>打开 Azure Cloud Shell

若要打开 Cloud Shell，请从代码块的右上角选择“试一试”。 也可以通过转到 [https://shell.azure.com/bash](https://shell.azure.com/bash) 在单独的浏览器标签页中启动 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后按 Enter 来运行它。

#### <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：

```
azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>创建私有云

提供资源组名称、私有云名称、位置和群集大小。

| 属性  | 说明  |
| --------- | ------------ |
| -g（资源组名称）     | 私有云资源的资源组的名称。        |
| -n（私有云名称）     | AVS 私有云的名称。        |
| --location     | 用于私有云的位置。         |
| --cluster-size     | 群集的大小。 最小值为 3。         |
| --network-block     | 用于私有云的 CIDR IP 地址网络块。 地址块不应与订阅和本地网络中的其他虚拟网络中使用的地址块重叠。        |
| --sku | SKU 值：AV36 |

```
azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="delete-a-private-cloud-azure-portal"></a>删除私有云（Azure 门户）

如果你不再需要某个 AVS 私有云，可将其删除。 删除私有云时，会一并删除所有群集及其所有组件。

为此，请在 Azure 门户中导航到你的私有云，然后选择“删除”。 在确认页上，输入私有云的名称以确认，然后选择“是”。

> [!CAUTION]
> 删除私有云是不可逆的操作。 删除私有云后，数据将无法恢复，因为此操作会终止所有正在运行的工作负载、组件，并销毁所有私有云数据和配置设置（包括公共 IP 地址）。 

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 创建 AVS 私有云
> * 验证部署的私有云

请继续学习下一教程，了解如何在为私有云群集设置本地管理的过程中，创建一个用于私有云的虚拟网络。

> [!div class="nextstepaction"]
> [创建虚拟网络](tutorial-configure-networking.md)
