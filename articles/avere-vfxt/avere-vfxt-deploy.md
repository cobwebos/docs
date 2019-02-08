---
title: 部署 Avere vFXT for Azure
description: 在 Azure 中部署 Avere vFXT 群集的步骤
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: da329b5c50fe7c39d9773743b40c2f990e298963
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296369"
---
# <a name="deploy-the-vfxt-cluster"></a>部署 vFXT 群集

此过程详述如何使用 Azure 市场提供的部署向导。 此向导自动使用 Azure 资源管理器模板来部署群集。 在窗体中输入参数并单击“创建”后，Azure 会自动完成以下步骤： 

* 创建群集控制器，该控制器是基本的 VM，其中包含部署和管理群集所需的软件。
* 设置资源组和虚拟网络基础结构，包括根据需要创建新元素。
* 创建群集节点 VM 并将其配置为 Avere 群集。
* 根据请求创建新的 Azure Blob 容器并将其配置为群集核心文件管理器。

按照本文档中的说明操作后，将获得虚拟网络、子网、控制器和 vFXT 群集，如下图所示：

![关系图，它所示的 VNET 包含可选 blob 存储以及一个子网，该子网包含三个标记为 vFXT 节点/vFXT 群集的分组 VM 和一个标记为群集控制器的 VM](media/avere-vfxt-deployment.png)

创建群集后，如果使用的是 Blob 存储，则应在虚拟网络中[创建存储终结点](#create-a-storage-endpoint-if-using-azure-blob)。 

在使用创建模板之前，请确保已具备以下必备项：  

1. [新订阅](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [订阅所有者权限](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [vFXT 群集的配额](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [自定义访问角色](avere-vfxt-prereqs.md#create-access-roles) - 必须创建基于角色的访问控制角色，以便将其分配到群集节点。 可以选择也为群集控制器创建自定义访问角色，但大多数用户会采用默认的所有者角色，这样可以获得与资源组所有者相对应的控制器特权。 如需更多详细信息，请阅读 [Azure 资源的内置角色](../role-based-access-control/built-in-roles.md#owner)。

有关群集部署的步骤和计划的详细信息，请阅读[计划 Avere vFXT 系统](avere-vfxt-deploy-plan.md)和[部署概述](avere-vfxt-deploy-overview.md)。

## <a name="create-the-avere-vfxt-for-azure"></a>创建 Avere vFXT for Azure

在 Azure 门户中访问创建模板，方法是：先搜索 Avere，然后选择“Avere vFXT for Azure 部署”。 <!-- xxx update if that name changes xxx --> 

<!-- **[XXX need production image of template deploy in search and/or entry page of template deploy XXX]** -->

单击“创建”即可开始创建。 

![Azure 市场，显示部署模板的第一页](media/avere-vfxt-deploy-first.png)

模板包含四个步骤/页面 - 两个信息收集页面，一个验证步骤和一个确认步骤。 

* 第一页重点介绍群集控制器 VM 的设置。 
* 第二页收集参数，这些参数适用于创建群集，以及适用于子网和存储之类的关联资源。 
* 第三页汇总设置并验证配置。 
* 第四页说明软件条款和条件，让你能够启动群集创建过程。 

## <a name="page-one-parameters---cluster-controller-information"></a>第一页的参数 - 群集控制器信息

部署模板的第一页收集有关群集控制器的信息。 

![部署模板的第一页](media/avere-vfxt-deploy-1.png)

填充以下信息：

* **群集控制器名称** - 设置群集控制器 VM 的名称。

* **控制器用户名** - 填充群集控制器 VM 的根用户名。 

* **身份验证类型** - 选择连接到控制器所需的密码或 SSH 公钥身份验证。 建议使用 SSH 公钥方法；如需帮助，请阅读[如何创建和使用 SSH 密钥](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)。

* **密码**或 **SSH 公钥** - 必须在后续字段中提供 RSA 公钥或密码，具体取决于所选身份验证类型。 此凭据与以前提供的用户名配合使用。

* **Avere 群集创建角色 ID** - 使用此字段指定群集控制器的访问控制角色。 默认值为内置角色：[所有者](../role-based-access-control/built-in-roles.md#owner)。 群集控制器的所有者特权仅限群集的资源组。 

  必须使用与角色相对应的全局唯一标识符。 与默认值（所有者）相对应的 GUID 为 8e3af657-a8ff-443c-a75c-2fe8c4bcb635。 若要查找自定义角色的 GUID，请使用以下命令： 

  ```azurecli
  az role definition list --query '[*].{roleName:roleName, name:name}' -o table --name 'YOUR ROLE NAME'
  ```

* **订阅** - 选择 Avere vFXT 的订阅。 

* **资源组** - 选择 Avere vFXT 群集的资源组，或者单击“新建”，然后输入新的资源组名称。 

* **位置** - 选择群集和资源的 Azure 位置。

完成后，单击“确定”。 

> [!NOTE]
> 如果希望群集控制器有一个面向公众的 IP 地址，请为群集创建一个新的虚拟网络，而不是选择一个现有的网络。 此设置在第二页上。

## <a name="page-two-parameters---vfxt-cluster-information"></a>第二页的参数 - vFXT 群集信息

可以使用部署模板的第二页来设置群集大小、节点类型、缓存大小、存储参数等设置。 

![部署模板的第二页](media/avere-vfxt-deploy-2.png)

* **Avere vFXT 群集节点计数** - 选择要在群集中使用的节点的数目。 至少 3 个节点，至多 12 个节点。 

* **群集管理密码** - 创建用于群集管理的密码。 若要登录到群集控制面板来监视群集并配置设置，需将此密码与用户名 ```admin``` 配合使用。

* **Avere 群集操作角色** - 指定群集节点的访问控制角色的名称。 这是在先决条件步骤中创建的自定义角色。 

  [创建群集节点访问角色](avere-vfxt-prereqs.md#create-the-cluster-node-access-role)中所述的示例将文件另存为 ```avere-operator.json```，相应的角色名称为 ```avere-operator```。

* **Avere vFXT 群集名称** - 为群集提供唯一名称。 

* **大小** - 指定创建群集节点时需要使用的 VM 类型。 

* **每个节点的缓存大小** - 群集缓存分布在多个群集节点上，因此 Avere vFXT 群集上的总缓存大小为每个节点的缓存大小乘以节点数。 

  建议的配置是：如果使用 Standard_D16s_v3 群集节点，则使用每个节点 1 TB 的设置；如果使用 Standard_E32s_v3 节点，则使用每个节点 4 TB 的设置。

* **虚拟网络** - 选择一个现有的 VNet 来托管群集，或者定义一个新的需要创建的 VNet。 

  > [!NOTE]
  > 如果创建新的 VNet，群集控制器就会有一个公共 IP 地址，供你访问新的专用网络。 如果选择现有的 VNet，则配置的群集控制器没有公共 IP 地址。 
  > 
  > 如果群集控制器上有公开显示的 IP 地址，则虽然方便用户对 vFXT 群集的访问，但会有一些安全风险。 
  >  * 用户可以将群集控制器上的公共 IP 地址用作跳转主机，从专用子网外部连接到 Avere vFXT 群集。
  >  * 如果未在控制器上设置公共 IP 地址，则必须使用其他跳转主机、VPN 连接或 ExpressRoute 来访问群集。 例如，在已配置 VPN 连接的虚拟网络中创建控制器。
  >  * 如果创建具有公共 IP 地址的控制器，则应使用网络安全组保护控制器 VM。 默认情况下，Avere vFXT for Azure 部署会创建一个网络安全组，仅限对具有公共 IP 地址的控制器的端口 22 进行入站访问。 可以通过锁定对 IP 源地址范围的访问来进一步保护系统，换句话说，只允许从你打算将其用于群集访问的计算机进行连接。

* **子网** - 从现有虚拟网络中选择一个子网，或者创建一个新的子网。 

* **使用 Blob 存储** - 选择是否创建新的 Azure Blob 容器并将其配置为新 Avere vFXT 群集的后端存储。 如果选择创建新容器，则必须为该容器提供存储帐户。 如果选择不创建新的 Blob 容器，则必须在创建群集后附加存储（有关说明，请阅读[配置存储](avere-vfxt-add-storage.md)）。 如果不希望创建新容器，请将此字段设置为 **false**。

* **存储帐户** - 如果创建新的 Azure Blob 容器，请输入存储帐户名称。 存储帐户必须是标准的常规用途 V2 帐户，并且配置了本地冗余的存储和热访问层。 [配置存储](avere-vfxt-add-storage.md#azure-storage-cloud-core-filer)一文更详细地介绍了存储帐户要求。

## <a name="validation-and-purchase"></a>验证和购买

第三页对配置进行汇总并验证参数。 验证成功后，请单击“确定”按钮继续。 

![部署模板的第三页 - 验证](media/avere-vfxt-deploy-3.png)

在第四页上单击“创建”按钮，接受条款并创建 Avere vFXT for Azure 群集。 

![部署模板的第四页 - 条款和条件、“创建”按钮](media/avere-vfxt-deploy-4.png)

群集部署需要 15-20 分钟。

## <a name="gather-template-output"></a>收集模板输出

Avere vFXT 模板在创建完群集以后，会输出有关新群集的某些重要信息。 

> [!TIP]
> 请务必复制模板输出中的管理 IP 地址。 需要该地址来管理群集。

若要查找此信息，请按照以下过程操作：

1. 转到群集控制器的资源组。

1. 在左侧单击“部署”，然后单击“microsoft-avere.vfxt-template”。

   ![资源组门户页面，其左侧选中“部署”，“部署名称”下的表中显示 microsoft-avere.vfxt-template](media/avere-vfxt-outputs-deployments.png)

1. 在左侧，单击“输出”。 复制每个字段中的值。 

   ![输出页面，在标签右侧的字段中显示 SSHSTRING、RESOURCE_GROUP、LOCATION、NETWORK_RESOURCE_GROUP、NETWORK、SUBNET、SUBNET_ID、VSERVER_IPs 和 MGMT_IP 的值](media/avere-vfxt-outputs-values.png)


## <a name="create-a-storage-endpoint-if-using-azure-blob"></a>创建存储终结点（如果使用的是 Azure Blob）

如果要将 Azure Blob 存储用于后端数据存储，应在虚拟网络中创建一个存储服务终结点。 此[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)使 Azure Blob 流量保留在本地，而不是通过 Internet 路由。

1. 在门户中，单击左侧的“虚拟网络”。
1. 为控制器选择 VNET。 
1. 单击左侧的“服务终结点”。
1. 单击顶部的“添加”。
1. 将服务保留为 ``Microsoft.Storage`` 并选择控制器的子网。
1. 在底部单击“添加”。

  ![带注释的 Azure 门户屏幕截图，注释标明创建服务终结点的步骤](media/avere-vfxt-service-endpoint.png)

## <a name="next-step"></a>后续步骤

运行群集并知道其管理 IP 地址以后，即可[连接到群集配置工具](avere-vfxt-cluster-gui.md)，以便启用支持、根据需要添加存储，以及自定义其他群集设置。
