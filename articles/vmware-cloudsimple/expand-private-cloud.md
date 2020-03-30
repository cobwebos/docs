---
title: 通过云简单私有云扩展 Azure VMware 解决方案
description: 描述如何扩展现有云简单私有云以在现有或新群集中添加容量
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a82ba1b433e62ed1c4b72b8e942d4ade29f26c4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025293"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>扩展云简单私有云

CloudSimple 提供了动态扩展私有云的灵活性。 您可以从较小的配置开始，然后根据需要扩展容量。 或者，您可以根据当前需求创建私有云，然后随着消费的增长而扩展。

私有云由一个或多个 vSphere 群集组成。 每个群集可以有 3 到 16 个节点。  扩展私有云时，会向现有群集添加节点或创建新群集。 要扩展现有群集，其他节点必须与现有节点的类型 （SKU） 相同。 对于创建新群集，节点可以是不同类型的。 有关私有云限制的详细信息，请参阅[云简单私有云概述](cloudsimple-private-cloud.md)文章中的限制部分。

使用**vCenter**上的默认数据中心创建私有云。  每个数据中心都用作顶级管理实体。  对于新群集，CloudSimple 提供了添加到现有数据中心或创建新数据中心的选择。

作为新群集配置的一部分，CloudSimple 配置 VMware 基础结构。  这些设置包括 vSAN 磁盘组的存储设置、VMware 高可用性和分布式资源调度程序 （DRS）。

私有云可以多次扩展。 仅当保持在总体节点限制内时，才能进行扩展。 每次扩展私有云时，您都会添加到现有群集或创建新群集。

## <a name="before-you-begin"></a>开始之前

必须先预配节点，然后才能扩展私有云。  有关预配节点的详细信息，请参阅[按云简单预配 VMware 解决方案的预配节点 - Azure](create-nodes.md)一文。  要创建新群集，必须至少具有同一 SKU 的三个可用节点。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 中的[https://portal.azure.com](https://portal.azure.com)Azure 门户。

## <a name="expand-a-private-cloud"></a>扩展私有云

1. [访问云简单门户](access-cloudsimple-portal.md)。

2. 打开 **"资源"** 页面并选择要为其扩展的私有云。

3. 在摘要部分中，单击 **"展开**"。

    ![扩展私有云](media/resources-expand-private-cloud.png)

4. 选择是扩展现有群集还是创建新的 vSphere 群集。 进行更改时，页面上的摘要信息将更新。

    * 要扩展现有群集，请单击"**展开现有群集**"。 选择要展开的群集，然后输入要添加的节点数。 每个群集最多可以有 16 个节点。
    * 要添加新群集，请单击"**创建新群集**"。 输入群集的名称。 选择现有数据中心，或输入名称以创建新数据中心。 选择节点类型。 创建新 vSphere 群集时可以选择不同的节点类型，但不能在扩展现有 vSphere 群集时选择。 选择节点数。 每个新群集必须至少有三个节点。

    ![扩展私有云 - 添加节点](media/resources-expand-private-cloud-add-nodes.png)

5. 单击"**提交**"以扩展私有云。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 上的 VMware VM](quickstart-create-vmware-virtual-machine.md)
* 了解有关[私有云](cloudsimple-private-cloud.md)的更多