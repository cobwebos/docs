---
title: 在 Microsoft Azure FXT Edge Filer 群集上装载客户端
description: NFS 客户端计算机如何装载 Azure FXT Edge Filer 混合存储缓存
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: b48d8d74843947c3e40dc80234560b0147be6eea
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542965"
---
# <a name="tutorial-mount-the-cluster"></a>教程：装载群集

本教程讲解如何将 NFS 客户端装载到 Azure FXT Edge Filer 群集。 客户端将会装载添加后端存储时分配的虚拟命名空间路径。 

本教程介绍： 

> [!div class="checklist"]
> * 在面向客户端的 IP 地址范围内对客户端进行负载均衡的策略
> * 如何基于面向客户端的 IP 地址和命名空间接合点构造装载路径
> * 要在装载命令中使用哪些参数

完成本教程大约需要 45 分钟。

## <a name="steps-to-mount-the-cluster"></a>装载群集的步骤

遵循以下步骤将客户端计算机连接到 Azure FXT Edge Filer 群集。

1. 确定如何在群集节点之间对客户端流量进行负载均衡。 有关详细信息，请阅读下面的[均衡客户端负载](#balance-client-load)。 
1. 确定要装载的群集 IP 地址和接合点路径。
1. 确定要装载的面向客户端的路径。
1. 使用适当的参数发出[装载命令](#use-recommended-mount-command-options)。

## <a name="balance-client-load"></a>均衡客户端负载

为了帮助均衡群集中所有节点之间的客户端请求，应将客户端装载到所有面向客户端的 IP 地址。 可通过多种方法将此任务自动化。

若要了解群集的轮循机制 DNS 负载均衡，请阅读[配置 Azure FXT Edge Filer 群集的 DNS](fxt-configure-network.md#configure-dns-for-load-balancing)。 若要使用此方法，必须维护一个 DNS 服务器，本系列文章不会介绍此操作。

适用于小型安装的更简单方法是在装载客户端时使用脚本分配整个范围内的 IP 地址。 

其他负载均衡方法可能适用于大型或复杂系统。 请咨询 Microsoft 代表，或提出支持请求以寻求帮助。 （Azure FXT Edge Filer 目前不支持 Azure 负载均衡器。） 

## <a name="create-the-mount-command"></a>创建装载命令 

``mount`` 命令将 Azure FXT Edge Filer 群集上的虚拟服务器 (vserver) 从客户端映射到本地文件系统上的路径。 

格式为 ``mount <FXT cluster path> <local path> {options}``

装载命令有三个元素： 

* 群集路径 - 下面所述的 IP 地址和命名空间接合点路径的组合
* 本地路径 - 客户端上的路径 
* 装载命令选项 -（[使用建议的装载命令选项](#use-recommended-mount-command-options)中已列出）

### <a name="create-the-cluster-path"></a>创建群集路径

群集路径是 vserver IP 地址加上命名空间接合点路径的组合   。 命名空间接合点是[添加存储系统](fxt-add-storage.md#create-a-junction)时定义的虚拟路径。

例如，如果使用 ``/fxt/files`` 作为命名空间路径，则客户端会将 *IP_address*:/fxt/files 装载到其本地载入点。 

![在命名空间路径字段中使用 /avere/files 添加“添加新交接点”对话框](media/fxt-mount/fxt-junction-example.png)

IP 地址是为 vserver 定义的面向客户端的 IP 地址之一。 可在群集控制面板中的两个位置找到面向客户端的 IP 范围：

* VServers 表（仪表板选项卡）  - 

  ![控制面板的仪表板选项卡，在图表下方的数据表中选择了 VServer 选项卡，并且带有 IP 地址部分](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* 面向客户端的网络设置页面  - 

  ![“设置”>“VServer”>“面向客户端的网络”配置页面，在特定虚拟服务器的表的“地址范围”部分周围有一个圆圈](media/fxt-mount/fxt-ip-addresses-settings.png)

将 IP 地址和命名空间路径相结合可以构成装载命令的群集路径。 

示例客户端装载命令：``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>创建本地路径

装载命令的本地路径由你决定。 可将所需的任何路径结构设置为虚拟命名空间的一部分。 设计可方便运行客户端工作流的命名空间和本地路径。 

有关面向客户端的命名空间的详细信息，请参阅《群集配置指南》中的[命名空间概述](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)。

在装载每个客户端时，除了路径以外，还要包含下面所述的[装载命令选项](#use-recommended-mount-command-options)。

### <a name="use-recommended-mount-command-options"></a>使用建议的装载命令选项

要确保无缝装载客户端，请在装载命令中传递这些设置和参数： 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| 必需设置 | |
--- | --- 
``hard`` | Azure FXT Edge Filer 群集的软装载与应用程序故障和可能的数据丢失相关联。 
``proto=netid`` | 此选项支持适当处理 NFS 网络错误。
``mountproto=netid`` | 此选项支持对装载操作的网络错误进行适当处理。
``retry=n`` | 设置 ``retry=30`` 以避免瞬时装载失败。 （建议在前景装载中使用不同的值。）

| 首选设置  | |
--- | --- 
``nointr``            | 如果客户端使用支持此选项的早期 OS 内核（2008 年 4 月之前），请使用此选项。 默认选项为“intr”。

## <a name="next-steps"></a>后续步骤

装载客户端后，可以测试工作流并开始使用群集。

如果需要将数据移到新的云核心文件管理器，请通过并行数据引入来利用缓存结构。 [将数据移到 vFXT 群集](https://docs.microsoft.com/azure/avere-vfxt/avere-vfxt-data-ingest)中介绍了一些策略。 （Avere vFXT for Azure 是基于云的产品，它使用的缓存技术非常类似于 Azure FXT Edge Filer。）

如果需要排查任何硬件问题，请参阅[监视 Azure FXT Edge Filer 硬件状态](fxt-monitor.md)。 
