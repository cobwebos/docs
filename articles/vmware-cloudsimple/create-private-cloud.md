---
title: 创建 Azure 的 VMware 解决方案通过 CloudSimple 私有云
description: 介绍如何创建 CloudSimple 私有云使用操作灵活性和连续性扩展到云的 VMware 工作负荷
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e5c03c1d8a865b792ce79e3e2b576a629b71e02c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333141"
---
# <a name="create-a-cloudsimple-private-cloud"></a>创建 CloudSimple 私有云

创建私有云可帮助您满足各种常见的网络基础结构的需求：

* **增长**。 如果你已达到硬件刷新点的现有基础结构，私有云，可展开所需的任何新硬件投资。

* **快速扩展**。 如果任何临时或计划外的容量需求出现，私有云，可创建更多的容量，且没有任何延迟。

* **增强的保护**。 通过三个或多个节点的私有云，您可以自动冗余和高可用性保护。

* **长期的基础结构需求**。 如果您的数据中心是最大程度上，或者想要重新组织结构来降低成本，私有云允许你停用数据中心迁移到基于云的解决方案同时保持兼容与你企业的操作。

在创建私有云时，可以获得单一 vSphere 群集和管理在该群集中创建 Vm。

## <a name="before-you-begin"></a>开始之前

可以创建您的私有云之前，必须预配节点。  有关节点的预配的详细信息，请参阅[针对通过 CloudSimple-Azure 的 VMware 解决方案预配节点](create-nodes.md)一文。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="access-the-cloudsimple-portal"></a>访问 CloudSimple 门户

访问[CloudSimple 门户](access-cloudsimple-portal.md)。

## <a name="create-a-new-private-cloud"></a>创建新的私有云

1. 上**资源**页上，单击**新的私有云**。

    ![创建私有云-如何启动](media/create-pc-button.png)

2. 选择要托管私有云资源的位置。

3. 选择 CS28 或 CS36 节点类型 you'ev 私有云的预配。 后一种包含的最大的计算和内存容量。

4. 私有云的选择节点的数。 您可以选择最多可用的节点数的 you'ev[预配](create-nodes.md)。

    ![创建私有云-基本设置](media/create-private-cloud-basic-info.png)

5. 单击“下一步:**高级选项**。

6. 输入 vSphere/vSAN 子网的 CIDR 范围。 请确保与任何本地或其他 Azure 子网 （虚拟网络） 或与网关子网的 CIDR 范围不重叠。  不要使用 Azure 虚拟网络上定义的任何 CIDR 范围。
    
    **CIDR 范围选项：**  /24、 / 23，/22 或 /21。 一个/24 CIDR 范围支持最多九个节点，/23 CIDR 范围支持最多 41 节点和，/22 和 /21 CIDR 范围支持最多 64 个节点 （最大节点数中私有云）。

    > [!CAUTION]
    > 私有云基础结构的情况下，vSphere/vSAN CIDR 范围中的 IP 地址保留供使用。  不在任何虚拟机上的此范围中使用的 IP 地址。

7. 单击“下一步:**查看和创建**。

8. 查看设置。 如果需要更改任何设置，请单击**上一步**。

9. 单击**创建**。

单击后将开始预配的私有云创建。  您可以监视从进度[任务](https://docs.azure.cloudsimple.com/activity/#tasks)CloudSimple 门户页上的。  预配可能需要 30 分钟到两个小时。  预配完成后，你将收到一封电子邮件。

## <a name="next-steps"></a>后续步骤

* [展开私有云](expand-private-cloud.md)