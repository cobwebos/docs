---
title: 教程 - 在虚拟网络中部署 Azure Spring Cloud
description: 在虚拟网络中部署 Azure Spring Cloud（v-net 注入）。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: 979ecf77fe53238dfd377c5fd2baf394de985c2f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90892891"
---
# <a name="tutorial-deploy-azure-spring-cloud-in-azure-virtual-network-vnet-injection"></a>教程：在 Azure 虚拟网络中部署 Azure Spring Cloud（VNet 注入）

本文适用于：✔️ Java ✔️ C#

本教程介绍如何在虚拟网络中部署 Azure Spring Cloud 服务实例。 这有时称为 VNet 注入。  

该部署支持：

* 从企业网络中的 Internet 隔离 Azure Spring Cloud 应用和服务运行时
* Azure Spring Cloud 与本地数据中心内的系统和/或其他虚拟网络中的 Azure 服务进行交互
* 授权客户控制 Azure Spring Cloud 的入站和出站网络通信

## <a name="prerequisites"></a>先决条件
必须根据说明[在 Azure 门户上注册资源提供程序](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal)或运行以下 az CLI 命令来注册 Azure Spring Cloud 资源提供程序 `Microsoft.AppPlatform`：

```azurecli
az provider register --namespace Microsoft.AppPlatform
```
## <a name="virtual-network-requirements"></a>虚拟网络要求
将 Azure Spring Cloud 服务实例部署到其中的虚拟网络必须满足以下要求：

* 位置：该虚拟网络必须与 Azure Spring Cloud 服务实例位于同一位置。
* 订阅：该虚拟网络必须与 Azure Spring Cloud 服务实例位于同一订阅中。
* **子网**：该虚拟网络必须包含两个专用于 Azure Spring Cloud 服务实例的子网： 
    * 一个用于服务运行时
    * 一个用于 Spring Boot 微服务应用程序。 
    * 这些子网和 Azure Spring Cloud 服务实例之间存在一对一关系。 需要为部署的每个服务实例使用一个新的子网，并且每个子网只能包含一个服务实例。
* **地址空间**：一个 CIDR 块最多为 /28（用于服务运行时子网），而另一个 CIDR 块最多为 /24（用于 Spring Boot 微服务应用程序子网）。
* **路由表**：子网不得有关联的现有路由表。

以下过程描述了虚拟网络的设置，以包含 Azure Spring Cloud 的实例。

## <a name="create-a-virtual-network"></a>创建虚拟网络
如果已有用于托管 Azure Spring Cloud 服务实例的虚拟网络，请跳过步骤 1、2 和 3。 可以从步骤 4 开始，为虚拟网络准备子网。

1. 在 Azure 门户菜单中，选择“创建资源”。 在 Azure 市场中，选择“网络” > “虚拟网络” 。

1. 在“创建虚拟网络”对话框中，输入或选择以下信息：

    |设置          |值                                             |
    |-----------------|--------------------------------------------------|
    |订阅     |选择订阅。                         |
    |资源组   |选择你的资源组，或新建一个资源组。  |
    |名称             |输入 azure-spring-cloud-vnet                   |
    |位置         |选择“美国东部”                                |

1. 单击“下一步:IP 地址 >”。 
 
1. 对于 IPv4 地址空间，输入 10.1.0.0/16。

1. 选择“添加子网”，然后输入 service-runtime-subnet 作为“子网名称”，输入 10.1.0.0/24 作为“子网地址范围” 。 然后单击“添加”  。

1. 再次选择“添加子网”，然后输入 apps-subnet 作为“子网名称”，输入 10.1.1.0/24 作为“子网地址范围” 。  单击“添加”。

1. 单击“查看 + 创建”。 将剩余的字段保留为默认值，然后单击“创建”。

## <a name="grant-service-permission-to-the-virtual-network"></a>向虚拟网络授予服务权限

选择之前创建的虚拟网络 azure-spring-cloud-vnet。

1. 选择“访问控制(IAM)”，然后选择“添加”>“添加角色分配” 。

    ![对 v-net 的访问控制](./media/spring-cloud-v-net-injection/access-control.png)

2. 在“添加角色分配”对话框中，输入或选择以下信息：

    |设置  |值                                             |
    |---------|--------------------------------------------------|
    |角色     |选择“所有者”                                  |
    |选择   |输入“Azure Spring Cloud 资源提供程序”      |

    然后选择“Azure Spring Cloud 资源提供程序”，并单击“保存”。

    ![向 v-net 授予 Azure Spring Cloud 资源提供程序](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

还可以通过运行以下 az CLI 命令来实现此目的

```azurecli
VIRTUAL_NETWORK_RESOURCE_ID=`az network vnet show \
    --name ${NAME_OF_VIRTUAL_NETWORK} \
    --resource-group ${RESOURCE_GROUP_OF_VIRTUAL_NETWORK} \
    --query "id" \
    --output tsv`

az role assignment create \
    --role "Owner" \
    --scope ${VIRTUAL_NETWORK_RESOURCE_ID} \
    --assignee e8de9221-a19c-4c81-b814-fd37c6caf9d2
```

## <a name="deploy-azure-spring-cloud-service-instance-in-the-virtual-network"></a>在虚拟网络中部署 Azure Spring Cloud 服务实例

1. 使用 https://ms.portal.azure.com 打开 Azure 门户。

1. 在顶部搜索框中，搜索“Azure Spring Cloud”，并从结果中选择“Azure Spring Cloud” 。

1. 在“Azure Spring Cloud”页上，选择“+ 添加” 。

1. 在 Azure Spring Cloud“创建”页中填写表单。 

1. 选择与虚拟网络相同的资源组和区域。

1. 对于“服务详细信息”下的“名称”，请选择“azure-spring-cloud-vnet” 。

1. 选择“网络”选项卡，然后选择以下选项：

    |设置                                |值                                             |
    |---------------------------------------|--------------------------------------------------|
    |在自己的虚拟网络中部署     |选择“是”                                    |
    |虚拟网络                        |选择“azure-spring-cloud-vnet”                  |
    |服务运行时子网                 |选择“service-runtime-subnet”                   |
    |Spring Boot 微服务应用子网   |选择“apps-subnet”                              |

    ![创建的“网络”选项卡](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. 单击“审阅并创建”。

1. 验证你的规范，并单击“创建”。

部署后，订阅中将创建另外两个资源组来托管 Azure Spring Cloud 服务实例的网络资源。  导航到“主页”，然后从顶部菜单项中选择“资源组”，以查找以下新资源组 。

名为 azure-spring-cloud-service-runtime_{service instance name}_{service instance region} 的资源组包含服务实例的服务运行时网络资源。

  ![服务运行时](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

名为 azure-spring-cloud-service-runtime_{service instance name}_{service instance region} 的资源组包含服务实例的 Spring Boot 微服务应用程序网络资源。

  ![应用资源组](./media/spring-cloud-v-net-injection/apps-resource-group.png)

这些网络资源连接到上面创建的虚拟网络。

  ![带已连接的设备的 V-net](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > 资源组由 Azure Spring Cloud 服务完全托管。 请不要手动删除或修改内部的任何资源。

## <a name="next-steps"></a>后续步骤

[在 VNet 中将应用程序部署到 Azure Spring Cloud 中](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>请参阅

- [在 VNET 中排除 Azure Spring Cloud 的故障](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [在 VNET 中运行 Azure Spring Cloud 的客户责任](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)
