---
title: 展开 Azure VMware CloudSimple 私有云解决方案
description: 介绍如何扩展现有的 CloudSimple 私有云以在现有或新群集中增加容量
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 293a09c57ca95e2774e44ff4bc9f9f2c31be2f49
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332767"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>展开 CloudSimple 私有云

CloudSimple 提供了动态扩展私有云的灵活性。 你可以开始使用更小的配置，然后展开根据需要更高的容量。 或者，可以创建私有云根据当前需求，然后展开随着使用量的增长。

私有云包含一个或多个 vSphere 群集。 每个群集可以具有 3 到 16 个节点。  当展开私有云，则将节点添加到现有群集或创建新的群集。 若要扩展现有的群集，其他节点必须与现有节点相同的类型 (SKU)。 有关如何创建新的群集，节点可以是不同的类型。 有关私有云限制的详细信息，请参阅限制中的部分[CloudSimple 私有云概述](cloudsimple-private-cloud.md)一文。

默认值创建私有云**数据中心**vCenter 上。  每个数据中心用作顶级管理实体。  对于新的群集，CloudSimple 提供了添加到现有数据中心或创建新的数据中心的选择。

作为新的群集配置的一部分，CloudSimple 配置 VMware 基础结构。  设置包括 vSAN 磁盘组、 VMware 高可用性和分布式资源调度 (DRS) 的存储设置。

私有云可以扩展多次。 仅当保留整体节点限制范围内时，才可进行扩展。 每次你展开私有云将添加到现有群集或新建一个。

## <a name="before-you-begin"></a>开始之前

您可以展开您的私有云之前，必须预配节点。  有关节点的预配的详细信息，请参阅[针对通过 CloudSimple-Azure 的 VMware 解决方案预配节点](create-nodes.md)一文。  有关如何创建新的群集，必须具有在同一 SKU 的至少三个可用节点。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="expand-a-private-cloud"></a>展开私有云

1. [访问 CloudSimple 门户](access-cloudsimple-portal.md)。

2. 打开**资源**页，然后选择你想要展开私有云。

3. 在摘要部分中，单击**展开**。

    ![展开私有云](media/resources-expand-private-cloud.png)

4. 选择是否要扩展现有群集或创建新的 vSphere 群集。 进行更改时，会更新页面上的摘要信息。

    * 若要展开现有群集，请单击**展开现有群集**。 选择你想要展开并输入要添加的节点数的群集。 每个群集可以有 16 个节点最多。
    * 若要添加新的群集，请单击**创建新群集**。 输入群集的名称。 选择现有的数据中心，或输入一个名称来创建新的数据中心。 选择的节点类型。 当创建新的 vSphere 群集，但未时扩展现有的 vSphere 群集，可以选择不同的节点类型。 选择节点的数。 每个新的群集必须具有至少三个节点。

    ![展开私有云-添加节点](media/resources-expand-private-cloud-add-nodes.png)

5. 单击**提交**以展开私有云。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 上的 VMware Vm](quickstart-create-vmware-virtual-machine.md)
* 详细了解[私有云](cloudsimple-private-cloud.md)