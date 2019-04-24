---
title: 部署 Avere vFXT for Azure
description: 在 Azure 中部署 Avere vFXT 群集的步骤
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: v-erkell
ms.openlocfilehash: 7ded66c29f12b8f68746726ca6c126bffbc51f0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60410209"
---
# <a name="deploy-the-vfxt-cluster"></a>部署 vFXT 群集

此过程详述如何使用 Azure 市场提供的部署向导。 此向导自动使用 Azure 资源管理器模板来部署群集。 在窗体中输入参数并单击“创建”后，Azure 会自动完成以下步骤：

* 创建群集控制器，这是基本的 VM，其中包含部署和管理群集所需的软件。
* 设置资源组和虚拟网络基础结构，包括创建新元素。
* 创建群集节点 Vm，并将它们配置为 Avere 群集。
* 如果请求，创建新的 Azure Blob 容器，并将其配置为群集核心文件管理器。

本文档中的说明进行操作后，将获得一个虚拟网络、 子网、 一个控制器和 vFXT 群集，如以下关系图中所示。 下图显示的可选 Azure Blob core 筛选器，其中包括新的 Blob 存储容器 （在新的存储帐户，不会显示） 和 Microsoft 存储空间的子网的服务终结点。 

![关系图显示三个同心矩形与 Avere 群集组件。 外部矩形标记为资源组，并包含标记为 Blob 存储 （可选） 六边形。 中的下一步矩形标记为虚拟网络：10.0.0.0/16，并且不包含任何独特的组件。 最内层矩形标记为 Subnet:10.0.0.0/24，并包含标记为群集控制器，堆栈的三个 Vm 标记为 vFXT 节点 （vFXT 群集），并标记为服务终结点六边形的 VM。 没有连接的服务终结点 （这是内部子网） 和 blob 存储 （这是外部的子网和 vnet 的资源组中） 的箭头。 通过子网和虚拟网络边界传递箭头。](media/avere-vfxt-deployment.png)  

在使用创建模板之前，请确保已具备以下必备项：  

1. [新订阅](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [订阅所有者权限](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [vFXT 群集的配额](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [存储服务终结点 （如果需要）](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) -必需的部署使用现有的虚拟网络和创建 blob 存储

有关群集部署的步骤和计划的详细信息，请阅读[计划 Avere vFXT 系统](avere-vfxt-deploy-plan.md)和[部署概述](avere-vfxt-deploy-overview.md)。

## <a name="create-the-avere-vfxt-for-azure"></a>创建 Avere vFXT for Azure

通过 Avere 搜索并选择"Azure ARM 模板 Avere vFXT"访问 Azure 门户中的创建模板。 

![显示 Azure 门户的浏览器窗口（带有面包屑导航“新建>市场>所有内容”）。 在所有功能页上，搜索字段中术语"avere"和第二个结果，"Avere vFXT Azure ARM 模板"的边框为红色以突出显示它。](media/avere-vfxt-template-choose.png)

阅读有关 Azure ARM 模板页 Avere vFXT 的详细信息之后, 单击**创建**开始。 

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

* **Avere vFXT 群集名称** - 为群集提供唯一名称。 

* **大小**-本部分介绍用于在群集节点的 VM 类型。 尽管只有一个推荐的选项，但**更改大小**链接将打开此实例类型和指向定价计算器的详细信息表。  

* **每个节点的缓存大小** - 群集缓存分布在多个群集节点上，因此 Avere vFXT 群集上的总缓存大小为每个节点的缓存大小乘以节点数。 

  建议的配置是 Standard_E32s_v3 节点每个节点使用 4 TB。

* **虚拟网络**-定义新的 vnet 来存放该群集，或选择现有 vnet 满足先决条件中所述[规划 Avere vFXT 系统](avere-vfxt-deploy-plan.md#resource-group-and-network-infrastructure)。 

  > [!NOTE]
  > 如果创建新的 VNet，群集控制器就会有一个公共 IP 地址，供你访问新的专用网络。 如果选择现有的 VNet，则配置的群集控制器没有公共 IP 地址。 
  > 
  > 如果群集控制器上有公开显示的 IP 地址，则虽然方便用户对 vFXT 群集的访问，但会有一些安全风险。 
  >  * 用户可以将群集控制器上的公共 IP 地址用作跳转主机，从专用子网外部连接到 Avere vFXT 群集。
  >  * 如果未在控制器上设置公共 IP 地址，则必须使用其他跳转主机、VPN 连接或 ExpressRoute 来访问群集。 例如，在已配置 VPN 连接的虚拟网络中创建控制器。
  >  * 如果创建具有公共 IP 地址的控制器，则应使用网络安全组保护控制器 VM。 默认情况下，Avere vFXT for Azure 部署会创建一个网络安全组，仅限对具有公共 IP 地址的控制器的端口 22 进行入站访问。 可以通过锁定对 IP 源地址范围的访问来进一步保护系统，换句话说，只允许从你打算将其用于群集访问的计算机进行连接。

  部署模板还将配置新的 vnet 与 Azure Blob 存储的存储服务终结点和锁定为仅 Ip 从群集子网的网络访问控制。 

* **子网** - 从现有虚拟网络中选择一个子网，或者创建一个新的子网。 

* **创建和使用 blob 存储**-选择**true**创建新的 Azure Blob 容器并将其配置为新的 Avere vFXT 群集的后端存储。 此选项还会创建新的存储帐户用作群集的群集子网内，Microsoft 存储服务终结点在同一资源组中。 
  
  如果您提供的现有虚拟网络，它必须具有存储服务终结点，在创建群集之前。 (有关详细信息，请阅读[规划 Avere vFXT 系统](avere-vfxt-deploy-plan.md)。)

  如果不希望创建新容器，请将此字段设置为 **false**。 在这种情况下，必须在创建群集后附加和配置存储。 有关说明，请参阅[配置存储](avere-vfxt-add-storage.md)。 

* **（新）存储帐户**-如果创建新的 Azure Blob 容器中，输入新的存储帐户的名称。 

## <a name="validation-and-purchase"></a>验证和购买

第三页汇总了配置和验证参数。 验证成功后，请单击“确定”按钮继续。 

![部署模板的第三页 - 验证](media/avere-vfxt-deploy-3.png)

在第 4 页上，输入任何所需的联系信息，然后单击**创建**按钮以接受条款并创建 Azure 群集 Avere vFXT。 

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

## <a name="next-step"></a>后续步骤

运行群集并知道其管理 IP 地址以后，即可[连接到群集配置工具](avere-vfxt-cluster-gui.md)，以便启用支持、根据需要添加存储，以及自定义其他群集设置。
