---
title: 概念-存储
description: 了解 Azure VMware 解决方案私有云中的主要存储功能。
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 02378040061080d3c9abbfafb26180c9d22e9073
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316811"
---
#  <a name="azure-vmware-solution-storage-concepts"></a>Azure VMware 解决方案存储概念

Azure VMware 解决方案私有云提供了本机的群集范围内的群集存储。 群集中每个主机的所有本地存储都用于 vSAN 数据存储，并且默认情况下，静态数据加密是可用的。 可以使用 Azure 存储资源扩展私有云的存储功能。

## <a name="vsan-clusters"></a>vSAN 群集

每个群集主机中的本地存储用作 vSAN 数据存储的一部分。 所有 diskgroups 都使用 1.6 TB 的 NVMe 缓存层，每个主机、基于 SSD 的容量为 15.4 TB。 群集的原始容量层大小为每个主机容量乘以主机数。 例如，四个主机群集会在 vSAN 容量层中提供 61.6 TB 的原始容量。

群集主机中的本地存储在群集范围的 vSAN 数据存储中使用。 所有数据存储都创建为私有云部署的一部分，可立即使用。 Cloudadmin 用户和 CloudAdmin 组中的所有用户都可以管理具有以下 vSAN 权限的数据存储：
- Datastore.AllocateSpace
- Datastore.Browse
- Datastore.Config
- DeleteFile
- FileManagement
- UpdateVirtualMachineMetadata

## <a name="data-at-rest-encryption"></a>静态数据加密

默认情况下，vSAN 数据存储使用静态数据加密。 加密解决方案基于 KMS，并支持用于密钥管理的 vCenter 操作。 密钥以加密方式存储，并由基于 HSM 的 Azure Key Vault 主密钥包装。 出于任何原因从群集中删除主机时，Ssd 上的数据会立即失效。

## <a name="scaling"></a>扩展

通过将主机添加到群集来扩展本机群集存储容量。 对于使用他的主机的群集，每个额外主机的原始群集范围容量增加了 15.4 TB。 使用 GP 主机构建的群集的原始容量增加了 7.7 TB，每增加一台主机。 在这两种类型的群集中，主机需要大约10分钟才能添加到群集。 有关缩放群集的说明，请参阅 [扩展私有云教程][tutorial-scale-private-cloud] 。

## <a name="azure-storage-integration"></a>Azure 存储集成

可以在私有云中运行的工作负荷上使用 Azure 存储服务。 Azure 存储服务包括存储帐户、表存储和 Blob 存储。 工作负荷到 Azure 存储服务的连接不会遍历 internet。 此连接提供额外的安全性，并使你能够在私有云工作负荷中使用基于 SLA 的 Azure 存储服务。

## <a name="next-steps"></a>后续步骤

下一步是了解 [私有云标识概念][concepts-identity]。

<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorial-scale-private-cloud]: ./tutorial-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md
