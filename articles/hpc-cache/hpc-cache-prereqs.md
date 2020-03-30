---
title: Azure HPC 缓存先决条件
description: 使用 Azure HPC 缓存的先决条件
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: rohogue
ms.openlocfilehash: 40d282ad30a800a5e5a36a8d2211ec8da7ce63ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271845"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Azure HPC 缓存的先决条件

在使用 Azure 门户创建新的 Azure HPC 缓存之前，请确保环境满足这些要求。

## <a name="azure-subscription"></a>Azure 订阅

建议付费订阅。

> [!NOTE]
> 在 GA 发布的头几个月中，Azure HPC 缓存团队必须将订阅添加到访问列表中，然后才能用于创建缓存实例。 此过程有助于确保每个客户从他们的缓存中获得高质量的响应。 填写[此表单](https://aka.ms/onboard-hpc-cache)以请求访问。

## <a name="network-infrastructure"></a>网络基础结构

在使用缓存之前，应设置两个与网络相关的先决条件：

* Azure HPC 缓存实例的专用子网
* DNS 支持，以便缓存可以访问存储和其他资源

### <a name="cache-subnet"></a>缓存子网

Azure HPC 缓存需要具有以下特性的专用子网：

* 子网必须至少有 64 个可用的 IP 地址。
* 子网无法承载任何其他 VM，即使对于客户端计算机等相关服务也是如此。
* 如果使用多个 Azure HPC 缓存实例，则每个实例都需要其自己的子网。

最佳做法是为每个缓存创建新子网。 作为创建缓存的一部分，您可以创建新的虚拟网络和子网。

### <a name="dns-access"></a>DNS 访问

缓存需要 DNS 访问其虚拟网络外部的资源。 根据所使用的资源，您可能需要设置自定义 DNS 服务器并配置该服务器和 Azure DNS 服务器之间的转发：

* 要访问 Azure Blob 存储终结点和其他内部资源，需要基于 Azure 的 DNS 服务器。
* 要访问本地存储，您需要配置可解析存储主机名的自定义 DNS 服务器。

如果只需要访问 Blob 存储，则可以将默认的 Azure 提供的 DNS 服务器用于缓存。 但是，如果需要访问其他资源，则应创建自定义 DNS 服务器并将其配置为将任何特定于 Azure 的解析请求转发到 Azure DNS 服务器。

一个简单的 DNS 服务器还可用于在所有可用的缓存装载点之间加载平衡客户端连接。

在[Azure 虚拟网络中的资源名称解析](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)中了解有关 Azure 虚拟网络和 DNS 服务器配置的更多信息。

## <a name="permissions"></a>权限

在开始创建缓存之前，请检查这些与权限相关的先决条件。

* 缓存实例需要能够创建虚拟网络接口 （NIC）。 创建缓存的用户必须在订阅中具有创建 NIC 的足够权限。

* 如果使用 Blob 存储，Azure HPC 缓存需要授权才能访问存储帐户。 使用基于角色的访问控制 （RBAC） 为缓存提供对 Blob 存储的访问权限。 需要两个角色：存储帐户参与者和存储 Blob 数据参与者。

  按照[添加存储目标](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)中的说明添加角色。

## <a name="storage-infrastructure"></a>存储基础架构

缓存支持 Azure Blob 容器或 NFS 硬件存储导出。 创建缓存后添加存储目标。

每种存储类型都有特定的先决条件。

### <a name="blob-storage-requirements"></a>Blob 存储要求

如果要将 Azure Blob 存储与缓存一起使用，则需要一个兼容的存储帐户和一个空 Blob 容器，或者一个容器，该容器填充 Azure HPC 缓存格式化的数据，如[将数据移动到 Azure Blob 存储](hpc-cache-ingest.md)中所述。

在尝试添加存储目标之前创建帐户。 添加目标时，可以创建新容器。

要创建兼容的存储帐户，请使用以下设置：

* 性能：**标准**
* 帐户类型：**存储V2（通用 v2）**
* 复制：**本地冗余存储(LRS)**
* 访问层（默认）：**热**

最好使用与缓存位于同一位置的存储帐户。
<!-- clarify location - same region or same resource group or same virtual network? -->

您还必须向缓存应用程序授予对 Azure 存储帐户的访问权限，如上文[的权限](#permissions)中所述。 按照["添加存储目标"](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)中的过程为缓存提供所需的访问角色。 如果您不是存储帐户所有者，则让所有者执行此步骤。

### <a name="nfs-storage-requirements"></a>NFS 存储要求

如果使用 NFS 存储系统（例如，本地硬件 NAS 系统），请确保它满足这些要求。 您可能需要与存储系统（或数据中心）的网络管理员或防火墙管理员合作，以验证这些设置。

> [!NOTE]
> 如果缓存对 NFS 存储系统的访问不足，则存储目标创建将失败。

有关详细信息包含在[故障排除 NAS 配置和 NFS 存储目标问题](troubleshoot-nas.md)中。

* **网络连接：** Azure HPC 缓存需要在缓存子网和 NFS 系统的数据中心之间进行高带宽网络访问。 建议[使用 ExpressRoute](https://docs.microsoft.com/azure/expressroute/)或类似访问。 如果使用 VPN，则可能需要将其配置为将 TCP MSS 夹在 1350 处，以确保大型数据包未被阻止。 读取[VPN 数据包大小限制](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions)，以解决 VPN 设置的其他帮助。

* **端口访问：** 缓存需要访问存储系统上的特定 TCP/UDP 端口。 不同类型的存储具有不同的端口要求。

  要检查存储系统的设置，请按照此过程操作。

  * 向存储`rpcinfo`系统发出命令以检查所需的端口。 下面的命令列出了端口，并在表中格式化相关结果。 （使用系统的 IP 地址代替 *<storage_IP>* 术语。

    可以从安装了 NFS 基础结构的任何 Linux 客户端发出此命令。 如果在群集子网中使用客户端，它还可以帮助验证子网和存储系统之间的连接。

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  确保``rpcinfo``查询返回的所有端口都允许来自 Azure HPC 缓存子网的无限制流量。

  * 除了命令返回的`rpcinfo`端口外，请确保这些常用端口允许入站和出站流量：

    | 协议 | 端口  | 服务  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | 恩洛克姆格 |
    | TCP/UDP  | 4046  | 已安装   |
    | TCP/UDP  | 4047  | status   |

  * 检查防火墙设置，以确保它们允许所有这些必需端口上的流量。 请务必检查 Azure 中使用的防火墙以及数据中心中的本地防火墙。

* **目录访问：** 在`showmount`存储系统上启用命令。 Azure HPC Cache 使用此命令检查存储目标配置是否指向有效的导出，并确保多个装载不访问相同的子目录（存在文件冲突的风险）。

  > [!NOTE]
  > 如果您的 NFS 存储系统使用 NetApp 的 ONTAP 9.2 操作系统，**请不要启用`showmount`**。 [请与 Microsoft 服务和支持联系](hpc-cache-support-ticket.md)以寻求帮助。

  在 NFS 存储目标故障排除文章中了解有关目录列表访问[的更多内容](troubleshoot-nas.md#enable-export-listing)。

* **根访问：** 缓存以用户 ID 0 身份连接到后端系统。 检查存储系统上的这些设置：
  
  * 启用 `no_root_squash`。 此选项可确保远程根用户可以访问 root 拥有的文件。

  * 检查导出策略，以确保它们不包括对来自缓存子网的根访问的限制。

  * 如果您的存储具有作为另一个导出的子目录的任何导出，请确保缓存具有对路径最低段的根访问权限。 有关详细信息，请阅读 NFS 存储目标故障排除文章中[目录路径上的根访问](troubleshoot-nas.md#allow-root-access-on-directory-paths)。

* NFS 后端存储必须是兼容的硬件/软件平台。 有关详细信息，请与 Azure HPC 缓存团队联系。

## <a name="next-steps"></a>后续步骤

* 从 Azure 门户[创建 Azure HPC 缓存实例](hpc-cache-create.md)
