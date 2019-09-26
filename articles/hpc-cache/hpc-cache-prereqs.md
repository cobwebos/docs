---
title: Azure HPC 缓存（预览版）先决条件
description: 使用 Azure HPC 缓存的先决条件
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: v-erkell
ms.openlocfilehash: fab85785ea183736b4012c349af143ef3a8c784a
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299918"
---
# <a name="prerequisites-for-azure-hpc-cache-preview"></a>Azure HPC 缓存的先决条件（预览版）

使用 Azure 门户创建新的 Azure HPC 缓存之前，请确保你的环境满足这些要求。

## <a name="azure-subscription"></a>Azure 订阅

建议使用付费订阅。

> [!NOTE]
> 在预览版本中，Azure HPC 缓存团队必须先将订阅添加到访问列表，然后才能将其用于创建缓存实例。 此过程有助于确保每个客户从其测试缓存获得高质量的响应能力。 填写[此窗体](https://aka.ms/onboard-hpc-cache)以请求访问。

## <a name="network-infrastructure"></a>网络基础结构

应设置两个与网络相关的先决条件，然后才能使用缓存：

* Azure HPC 缓存实例的专用子网
* DNS 支持，以便缓存可以访问存储和其他资源

### <a name="cache-subnet"></a>缓存子网

Azure HPC 缓存需要具有以下特性的专用子网：

* 子网必须具有至少64的可用 IP 地址。
* 子网不能托管任何其他 Vm，甚至是适用于客户端计算机的相关服务。
* 如果使用多个 Azure HPC 缓存实例，则每个实例都需要其自己的子网。

最佳做法是为每个缓存创建一个新的子网。 创建缓存时，可以创建新的虚拟网络和子网。

### <a name="dns-access"></a>DNS 访问

缓存需要 DNS 来访问其虚拟网络外部的资源。 根据所使用的资源，可能需要设置自定义 DNS 服务器并配置该服务器与 Azure DNS 服务器之间的转发：

* 若要访问 Azure Blob 存储终结点和其他内部资源，需要基于 Azure 的 DNS 服务器。
* 若要访问本地存储，需配置可解析存储主机名的自定义 DNS 服务器。

如果只需要访问 Blob 存储，则可以使用 Azure 提供的默认 DNS 服务器作为缓存。 但是，如果需要访问其他资源，则应创建自定义 DNS 服务器并将其配置为将任何特定于 Azure 的解析请求转发到 Azure DNS 服务器。 （也可以使用简单的 DNS 服务器对所有可用缓存装载点之间的客户端连接进行负载平衡。）

若要详细了解 azure 虚拟网络中的资源，请参阅[名称解析中的](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)azure 虚拟网络和 DNS 服务器配置。

## <a name="permissions"></a>权限

请先检查与权限相关的先决条件，然后再开始创建缓存。

* 缓存实例需要能够创建虚拟网络接口（Nic）。 创建缓存的用户必须在订阅中具有足够的权限才能创建 Nic。
<!-- There are several ways to authorize this access; read [Additional prerequisites](media/preview-prereqs.md) to learn more. -->

* 如果使用 Blob 存储，Azure HPC 缓存需要授权才能访问存储帐户。 你可以使用基于角色的访问控制（RBAC）来授予缓存对 Blob 存储的访问权限。 需要两个角色：存储帐户参与者和存储 Blob 数据参与者。 按照[添加存储目标](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)中的说明添加角色。

## <a name="storage-infrastructure"></a>存储基础结构

缓存支持 Azure Blob 容器或 NFS 硬件存储导出。 你可以在创建缓存时定义存储目标，但也可以在以后添加存储。

每个存储类型都具有特定的先决条件。 

### <a name="nfs-storage-requirements"></a>NFS 存储要求

如果使用本地硬件存储，则缓存需要对其子网中的数据中心具有高带宽的网络访问权限。 建议使用[ExpressRoute](https://docs.microsoft.com/azure/expressroute/)或类似的访问。

NFS 后端存储必须是兼容的硬件/软件平台。 有关详细信息，请联系 Azure HPC 缓存团队。

### <a name="blob-storage-requirements"></a>Blob 存储要求

如果要将 Azure Blob 存储与缓存一起使用，则需要一个兼容的存储帐户，以及一个使用 Azure HPC 缓存格式化数据填充的空 Blob 容器或容器，如[将数据移动到 Azure Blob 存储](hpc-cache-ingest.md)中所述。

请先创建帐户和容器，然后再尝试将其添加为存储目标。

若要创建兼容的存储帐户，请使用以下设置：

* 性能：**标准**
* 帐户类型：**StorageV2 （常规用途 v2）**
* 复制：**本地冗余存储 (LRS)**
* 访问层（默认）：**热访问层**

最好使用与缓存位于同一位置的存储帐户。
<!-- need to clarify location - same region or same resource group or same virtual network? -->

还必须为缓存应用程序授予对 Azure 存储帐户的访问权限。 按照[添加存储目标](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)中的说明，为缓存授予访问角色存储帐户参与者和存储 Blob 数据参与者的缓存。 如果你不是存储帐户所有者，请让所有者执行此步骤。

## <a name="next-steps"></a>后续步骤

* 从 Azure 门户[创建 AZURE HPC 缓存实例](hpc-cache-create.md)
