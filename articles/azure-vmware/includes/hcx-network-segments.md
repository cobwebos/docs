---
title: VMware HCX 网段
description: VMware HCX 需要四个网络。
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: f01aba13d688dfed573acfc8c4344511fbfd9fa8
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578641"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

VMware HCX 需要四个网络：

- **管理网络：** 通常，它与 vSphere 群集上使用的管理网络相同。  至少要在此网段上为 VMware HCX 标识两个 IP（可能需要更多数量，具体取决于你的部署）。

- **vMotion 网络：** 通常，它与 vSphere 群集上用于 vMotion 的网络相同。  至少要在此网段上为 VMware HCX 标识两个 IP（可能需要更多数量，具体取决于你的部署）。  

   必须在分布式虚拟交换机或 vSwitch0 上公开 vMotion 网络， 否则请修改环境。

   > [!NOTE]
   > 如果此网络未路由（专用），则没有问题。

- **上行网络：** 需要为 VMware HCX 上行创建一个新网络，并通过端口组将其扩展到 vSphere 群集。  至少要在此网段上为 VMware HCX 标识两个 IP（可能需要更多数量，具体取决于你的部署）。  

   > [!NOTE]
   > 推荐的方法是创建一个 /29 网络，但是任何网络大小都可以。

- **复制网络：** 需要为 VMware HCX 复制创建一个新网络，并通过端口组将该网络扩展到 vSphere 群集。  至少要在此网段上为 VMware HCX 标识两个 IP（可能需要更多数量，具体取决于你的部署）。

   > [!NOTE]
   > 推荐的方法是创建一个 /29 网络，但是任何网络大小都可以。