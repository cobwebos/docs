---
title: Azure VMware 解决方案，CloudSimple 2020 年9月更新
description: 本文介绍此维护操作过程中的预期内容，并对私有云进行更改。
author: dikamath
ms.author: dikamath
ms.date: 09/3/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
manager: dikamath
ms.openlocfilehash: ae9c1ba5259e95ed030d7099e5dafe2d4f7935b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89447792"
---
# <a name="azure-vmware-solution-by-cloudsimple-september-2020-update"></a>Azure VMware 解决方案，CloudSimple 2020 年9月更新

9月将执行 Azure VMware 解决方案服务的重要更新。 作为维护的一部分发送的电子邮件通知将包括维护时间线。 在本文中，你将了解在此维护操作过程中会发生的情况以及对私有云的更改。

> [!NOTE]
> 这是非中断性升级。 在升级过程中，你可能会看到其中一个冗余组件关闭。

## <a name="vmware-infrastructure-upgrade"></a>VMware 基础结构升级

私有云的 VMware 基础结构将更新为较新的版本。 这包括对 vCenter、ESXi、NSX-T 和混合云扩展 (HCX) 组件的更新， (如果已将) 部署到私有云。

在升级过程中，会将一个新节点添加到私有云，然后将节点置于维护模式下进行升级操作。 这可确保在升级过程中保留私有云的容量和私有云的可用性。 在升级 VMware 组件期间，你可能会看到在 vCenter web UI 上显示的警报。 警报是由服务操作团队执行的维护操作的一部分。

**组件版本**

- ESXi 6.7 U3
- vCenter 6.7 U3
- vSAN 6。7
- NSX 数据中心2.5。1
- HCX 3.5。2

## <a name="datacenter-updates"></a>数据中心更新

此更新包括对数据中心基础结构的更新。 在维护期间将执行非中断性更新。 你会注意到，在更新过程中会降低冗余性。 可能会在私有云 VMware 基础结构、ExpressRoute 线路、GlobalReach 连接和任何与链接可用性相关的站点到站点 VPN 设备上生成警报。 在更新过程中，这是正常现象，因为组件将作为更新的一部分重新启动。

-   如果将站点到站点 VPN 部署为 (非 HA) 的单个实例，则可能需要重新建立 VPN 连接。

-   如果使用点到站点 VPN 连接，则必须重新建立 VPN 连接。

## <a name="post-update"></a>更新后

更新完成后，应会看到 VMware 组件的更新版本。 如果你注意到任何问题或有任何疑问，请通过提出 [支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)与我们的支持团队联系。
