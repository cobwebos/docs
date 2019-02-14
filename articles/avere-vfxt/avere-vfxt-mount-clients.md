---
title: 装载 Avere vFXT - Azure
description: 如何使用 Avere vFXT for Azure 装载客户端
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 41065b4ac6bc486e204c2bfd72b78ba8722270c4
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55809154"
---
# <a name="mount-the-avere-vfxt-cluster"></a>装载 Avere vFXT 群集  

按照以下步骤将客户端计算机连接到 vFXT 群集。

1. 确定如何在群集节点之间对客户端流量进行负载均衡。 有关详细信息，请阅读下面的[均衡客户端负载](#balance-client-load)。 
1. 确定要装载的 IP 地址和交接点路径。
1. 使用适当的参数发出[装载命令](#mount-command-arguments)。

## <a name="balance-client-load"></a>均衡客户端负载

为了帮助均衡群集中所有节点之间的客户端请求，应将客户端装载到所有面向客户端的 IP 地址。 有几种简单的方法可以自动执行此任务。

> [!TIP] 
> 其他负载均衡方法可能适用于大型或复杂系统；请[开具支持票证](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt)寻求帮助。）
> 
> 如果偏向于使用 DNS 服务器进行自动服务器端负载均衡，则必须在 Azure 中设置和管理自己的 DNS 服务器。 在这种情况下，可以根据以下文档中的操作为 vFXT 群集配置轮询 DNS：[Avere 群集 DNS 配置](avere-vfxt-configure-dns.md)。

### <a name="sample-balanced-client-mounting-script"></a>示例均衡客户端装载脚本

此代码示例使用客户端 IP 地址作为随机元素，将客户端分发到所有 vFXT 群集的可用 IP 地址。

```bash
function mount_round_robin() {
    # to ensure the nodes are spread out somewhat evenly the default 
    # mount point is based on this node's IP octet4 % vFXT node count.
    declare -a AVEREVFXT_NODES="($(echo ${NFS_IP_CSV} | sed "s/,/ /g"))"
    OCTET4=$((`hostname -i | sed -e 's/^.*\.\([0-9]*\)/\1/'`))
    DEFAULT_MOUNT_INDEX=$((${OCTET4} % ${#AVEREVFXT_NODES[@]}))
    ROUND_ROBIN_IP=${AVEREVFXT_NODES[${DEFAULT_MOUNT_INDEX}]}

    DEFAULT_MOUNT_POINT="${BASE_DIR}/default"

    # no need to write again if it is already there
    if ! grep --quiet "${DEFAULT_MOUNT_POINT}" /etc/fstab; then
        echo "${ROUND_ROBIN_IP}:${NFS_PATH}    ${DEFAULT_MOUNT_POINT}    nfs hard,nointr,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
        mkdir -p "${DEFAULT_MOUNT_POINT}"
        chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
    fi
    if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
        retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
    fi   
} 
```

上述函数是 [Avere vFXT 示例](https://github.com/Azure/Avere#tutorials)站点中可用的 Batch 示例的一部分。

## <a name="create-the-mount-command"></a>创建装载命令 

> [!NOTE]
> 如果在创建 Avere vFXT 群集时未创建新的 Blob 容器，请在尝试装入客户端之前按照[配置存储](avere-vfxt-add-storage.md)中的步骤进行操作。

``mount`` 命令将 vFXT 群集上的虚拟服务器 (vserver) 从客户端映射到本地文件系统上的路径。 格式为 ``mount <vFXT path> <local path> {options}``

装载命令有三个元素： 

* vFXT 路径 -（下面所述的 IP 地址和命名空间交接点路径的组合）
* 本地路径 - 客户端上的路径 
* 装载命令选项 - （在[装载命令参数](#mount-command-arguments)中列出）

### <a name="junction-and-ip"></a>交接点和 IP

vserver 路径是其 IP 地址加上命名空间交接点的路径的组合。 命名空间交接点是在添加存储系统时定义的虚拟路径。

如果群集是使用 Blob 存储创建的，则命名空间路径为 `/msazure`

示例： ``mount 10.0.0.12:/msazure /mnt/vfxt``

如果在创建群集后添加了存储，则命名空间交接点路径对应于在创建交接点时在命名空间路径中设置的值。 例如，如果使用 ``/avere/files`` 作为命名空间路径，则客户端会将 IP_address:/avere/files 装载到其本地装载点。

![在命名空间路径字段中使用 /avere/files 添加“添加新交接点”对话框](media/avere-vfxt-create-junction-example.png)


IP 地址是为 vserver 定义的面向客户端的 IP 地址之一。 可在 Avere 控制面板中的两个位置找到面向客户端的 IP 范围：

* VServers 表（仪表板选项卡） - 

  ![Avere 控制面板的仪表板选项卡，在图表下方的数据表中选择了 VServer 选项卡，并且带有 IP 地址部分](media/avere-vfxt-ip-addresses-dashboard.png)

* 面向客户端的网络设置页面 - 

  ![“设置”>“VServer”>“面向客户端的网络”配置页面，在特定虚拟服务器的表的“地址范围”部分周围有一个圆圈](media/avere-vfxt-ip-addresses-settings.png)

除了路径之外，在安装每个客户端时还要包括下面描述的[装载命令参数](#mount-command-arguments)。

### <a name="mount-command-arguments"></a>装载命令参数

要确保无缝装载客户端，请在装载命令中传递这些设置和参数： 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``


| 必需设置 | |
--- | --- 
``hard`` | 对 vFXT 群集的软装载与应用程序故障和可能的数据丢失相关联。 
``proto=netid`` | 此选项支持适当处理 NFS 网络错误。
``mountproto=netid`` | 此选项支持对装载操作的网络错误进行适当处理。
``retry=n`` | 设置 ``retry=30`` 以避免瞬时装载失败。 （建议在前景装载中使用不同的值。）

| 首选设置  | |
--- | --- 
``nointr``            | 对于具有支持此选项的旧内核（2008 年 4 月之前）的客户端，首选选项“nointr”。 请注意，选项“intr”是默认选项。


## <a name="next-steps"></a>后续步骤

装入客户端后，可以使用它们填充后端数据存储（核心文件管理器）。 请参阅以下文档，了解有关其他设置任务的更多信息：

* [将数据移动到群集核心文件管理器](avere-vfxt-data-ingest.md) - 如何使用多个客户端和线程来有效地上传数据
* [自定义群集优化](avere-vfxt-tuning.md) - 定制群集设置以适应工作负载
* [管理群集](avere-vfxt-manage-cluster.md) - 如何启动或停止群集并管理节点
