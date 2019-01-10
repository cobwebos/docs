---
title: 管理 Avere vFXT 群集 - Azure
description: 如何管理 Avere 群集 - 添加或删除节点、重启、停止或销毁 vFXT 群集
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: a47b18972e945e495e5a5d3dd90e383390612865
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2019
ms.locfileid: "54189603"
---
# <a name="manage-the-avere-vfxt-cluster"></a>管理 Avere vFXT 群集

创建群集后，可能需要添加群集节点或是停止或重启群集。 项目完成后，需了解如何永久停止和删除群集。 

群集管理任务不同，可能需使用 Avere 控制面板、vfxt.py 群集创建脚本或 Azure 门户来执行此操作。 

此表概述了可用于每项任务的工具。 

| 操作 | Avere 控制面板 | vfxt.py  | Azure 门户 |
| --- | --- | --- | --- |
| 添加群集节点 | 否 | 是 | 否 |
| 删除群集节点 | 是 | 否 | 否 |
| 停止群集节点 | 是（还可重新启动服务或重启） | 否 | 从门户关闭节点 VM 被视为节点出现故障 |
| 启动已停止的节点 | 否 | 否 | 是 |
| 销毁单个群集节点 | 否 | 否 | 是 |
| 重启群集 |  |  |  |
| 安全关闭或停止群集 | 是 | 是 | 否 |
| 销毁群集  | 否 | 是 | 是，但无法保证数据完整性 |

下方详细介绍了每项工具。

## <a name="about-stopped-instances-in-azure"></a>关于 Azure 中已停止的实例

关闭或停止任何 Azure VM 时，将停止产生计算费用，但仍必须为其存储付费。 如果关闭了 vFXT 节点或整个 vFXT 群集且不打算再重启，则应使用 Azure 门户删除相关 VM。 

在 Azure 门户中，已停止的节点（可重启）在 Azure 门户中显示为“已停止”状态；已删除的节点显示为“已停止（已取消分配）”状态，且不再产生计算或存储费用。

在删除 VM 之前，通过使用 Avere 控制面板或 vfxt.py 选项来停止或关闭群集，确保所有更改的数据都已从缓存写入后端存储。

## <a name="manage-the-cluster-with-avere-control-panel"></a>使用 Avere 控制面板管理群集 

Avere 控制面板可用于执行以下任务： 

* 停止或重启单个节点
* 从群集删除节点
* 停止或重启整个群集

Avere 控制面板优先考虑数据完整性，因此在尝试进行可能造成损坏的操作前，会将已更改的数据写入后端存储。 这使得它比 Avere 门户更加安全。 

从 Web 浏览器访问 Avere 控件面板。 如需帮助，请遵循[访问 vFXT 群集](avere-vfxt-cluster-gui.md)中的说明。

### <a name="manage-nodes-with-avere-control-panel"></a>使用 Avere 控制面板管理节点

FXT 节点设置页面有用于管理单独的节点的控件。

若要关闭、重启或删除节点，请在“FXT 节点”页面的列表中找到该节点，然后单击其“操作”列中的相应按钮。

> [!NOTE] 
> 活动节点的数量发生变化时，IP 地址可能会在群集节点之间移动。

请参阅 Avere 群集设置指南中的[群集 > FXT节点](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>)，了解详细信息。

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>使用 Avere 控制面板停止或重启群集

“系统维护”设置页面有重启群集服务、重启群集或安全地关闭群集的命令。 请参阅[管理 > 系统维护](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>)（位于 Avere 群集设置指南中），了解详细信息。

关闭群集时，它会先向“仪表板”选项卡发送状态消息。 片刻后，Avere 控制面板会话将停止响应，这意味着群集已关闭。

## <a name="manage-the-cluster-with-vfxtpy"></a>使用 vfxt.py 管理群集 

vfxt.py 脚本可用于执行以下群集管理任务：

* 向群集添加新节点
* 停止或启动群集  
* 销毁群集

与 Avere 控制面板一样，vfxt.py 操作试图在关闭或销毁群集或节点前确保将已更改的数据永久存储在后端存储上。 这使得它比 Avere 门户更加安全。

vfxt.py 预安装在群集控制器 VM 上。 <!-- (If you want to install it on another system, refer to https://github.com/Azure/AvereSDK) xxx change when this repo goes  public -->

GitHub 上提供了完整 vfxt.py 使用指南：[使用 vfxt.py 管理云群集](https://github.com/azure/averesdk/blob/master/docs/README.md)

### <a name="add-cluster-nodes-with-vfxtpy"></a>使用 vfxt.py 添加群集节点

用于添加群集节点的示例命令脚本包含在群集控制器中。 找到控制器上的 ``./add-nodes``并在编辑器中将其打开，以使用群集信息对其进行自定义。 

群集必须处于运行状态方可使用此命令。 

提供以下值： 

* 群集资源组名称，以及网络和存储资源的资源组名称（与群集资源组名称不一致时）
* 群集位置
* 群集网络和子网 
* 群集节点访问角色 
* 群集管理 IP 地址和管理密码 
* 要添加的节点数（1、2 或 3）
* 节点实例类型和缓存大小值 

如果不使用原型，则必须构造如下所示的命令，其中包括上述所有信息。 

```bash
   vfxt.py --cloud-type azure --from-environment \
   --resource-group GROUP_NAME \ 
   [--network-resource-group GROUP_NAME --storage-resource-group GROUP_NAME]  \
   --location LOCATION --azure-network NETWORK_NAME --azure-subnet SUBNET_NAME \
   --add-nodes --nodes NODE_COUNT \
   --management-address CLUSTER_IP --admin-password ADMIN_PASSWORD \
   --instance-type TYPE --node-cache-size SIZE \
   --azure-role ROLE_NAME \
   --log ~/vfxt.log
```

有关详细信息，请参阅 vfxt.py 用法指南中的[将节点添加到群集](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster)。

### <a name="stop-a-cluster-with-vfxtpy"></a>使用 vfxt.py 停止群集

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>使用 vfxt.py 启动已停止的群集

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```    

由于群集已停止，因此必须传递实例标识符以指定群集节点。 请参阅 vfxt.py 用法指南中的[指定要修改的群集](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify)，了解详细信息。

### <a name="destroy-a-cluster-with-vfxtpy"></a>使用 vfxt.py 销毁群集

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

如果不想写入群集缓存中更改的数据，则可使用选项 ``--quick-destroy``。

请参阅 [vfxt.py 用法指南](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>)，了解其他信息。  

## <a name="manage-cluster-vms-from-the-azure-portal"></a>通过 Azure 门户管理群集 VM 

Azure 门户可用于单独销毁群集 VM，但如果先前未完全关闭群集，则无法保证数据完整性。 

Azure 门户可用于执行以下群集管理任务： 

* 启动已停止的 vFXT 节点
* 停止单个 vFXT 节点（群集将此解释为节点出现故障）
* 如果无需确保群集缓存中的已更改数据是否写入核心文件管理器，则可销毁 vFXT 群集。
* 在 vFXT 节点和其他群集资源安全关闭后将其永久删除

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>通过 Azure 门户重启 vFXT 实例

如果要重启已停止的节点，必须使用 Azure 门户。 在左侧菜单中选择“虚拟机”，然后单击列表中的 VM 名称，打开其概述页面。

单击概述页面顶部的“启动”按钮，重新激活 VM。

![显示用于启动已停止 VM 的选项的 Azure 门户屏幕](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>删除群集节点

如果要从 vFXT 群集删除一个节点，但保留群集的其余部分，则应首先使用 Avere 控制面板[从群集移除该节点](#manage-nodes-with-avere-control-panel)。

> [!CAUTION]
> 如果未先将节点从 vFXT 群集中移除就删除该节点，则数据可能会丢失。

若要永久销毁作为 vFXT 节点的一个或多个实例，请使用 Azure 门户。
在左侧菜单中选择“虚拟机”，然后单击列表中的 VM 名称，打开其概述页面。

单击概述页面顶部的“删除”按钮，永久性销毁 VM。

此方法可用于在安全关闭群集节点后将其永久删除。 

### <a name="destroy-the-cluster-from-the-azure-portal"></a>通过 Azure 门户销毁群集

> [!NOTE] 
> 如果要将缓存中留存的任何客户端更改写入后端存储，请使用 vfxt.py `--destroy` 选项，或使用 Avere 控制面板彻底关闭群集，之后再从 Azure 门户删除节点实例。

可通过在 Azure 门户中删除节点实例以将其永久销毁。 可按上述方法一一删除，也可使用“虚拟机”页面查找所有群集 VM，通过复选框将其选中，然后单击“删除”按钮，一次性将其删除。

![门户中按“群集”一词筛选的 VM 列表，其中选中并突出显示了四个 VM 中的三个](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>从 Azure 门户中删除其他群集资源

如果创建了专用于 vFXT 群集的其他资源，则可能需要在销毁群集时将其删除。 不应销毁包含所需数据的元素，或与其他项目共享的任何项。

除删除群集节点外，请考虑删除以下组件： 

* 群集控制器 VM
* 与群集节点关联的数据磁盘
* 网络接口和与群集组件关联的公共 IP
* 虚拟网络
* 存储帐户（仅当它们不包含重要数据时）
* 可用性集 

![Azure 门户“所有资源”列表，其中显示了为测试群集创建的资源](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>通过 Azure 门户删除群集资源组

如果创建了专用于群集的资源组，则可通过销毁资源组来销毁群集的所有相关资源。 

> [!Caution] 
> 只有在确定组中没有任何有价值的内容时方可销毁资源组。 例如，确保已从资源组的所有存储容器中移出了所需数据。  

若要删除资源组，请单击门户左侧菜单中的“资源组”，然后筛选资源组列表，查找为 vFXT 群集创建的资源组。 选择资源组，然后单击面板右侧的三个点。 选择“删除资源组”。 门户将要求你确认是否删除，此操作不可撤销。  

![显示“删除资源组”操作的资源组](media/avere-vfxt-delete-resource-group.png)
