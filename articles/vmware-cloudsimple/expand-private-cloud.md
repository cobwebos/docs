---
title: 通过 CloudSimple 私有云展开 Azure VMware 解决方案
description: 介绍如何扩展现有的 CloudSimple 私有云, 以在现有群集或新群集中添加容量
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a82ba1b433e62ed1c4b72b8e942d4ade29f26c4a
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816159"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>扩展 CloudSimple 私有云

CloudSimple 提供动态扩展私有云的灵活性。 你可以从较小的配置开始, 然后在需要更大容量时进行扩展。 或者, 你可以根据当前需求创建一个私有云, 然后随着消耗的增加而扩展。

私有云由一个或多个 vSphere 群集组成。 每个群集可以有3到16个节点。  展开私有云时, 可以向现有群集添加节点或创建新群集。 若要展开现有群集, 附加节点必须与现有节点的类型相同 (SKU)。 若要创建新群集, 节点可以是不同的类型。 有关私有云限制的详细信息, 请参阅[CloudSimple 私有云概述](cloudsimple-private-cloud.md)一文中的限制部分。

在 vCenter 上创建一个具有默认**数据中心**的私有云。  每个数据中心都作为顶级管理实体。  对于新群集, CloudSimple 提供了将添加到现有数据中心或创建新数据中心的选项。

作为新群集配置的一部分, CloudSimple 配置 VMware 基础结构。  这些设置包括 vSAN 磁盘组的存储设置、VMware 高可用性和分布式资源计划程序 (DRS)。

私有云可以扩展多次。 只有在整个节点限制范围内时, 才可以进行扩展。 每次展开要添加到现有群集的私有云时, 或创建一个新的群集。

## <a name="before-you-begin"></a>开始之前

必须先设置节点, 然后才能扩展私有云。  有关预配节点的详细信息, 请参阅[通过 CloudSimple 为 VMware 解决方案预配节点-Azure 一](create-nodes.md)文。  若要创建新群集, 必须有至少三个相同 SKU 的可用节点。

## <a name="sign-in-to-azure"></a>登录  Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="expand-a-private-cloud"></a>展开私有云

1. [访问 CloudSimple 门户](access-cloudsimple-portal.md)。

2. 打开 "**资源**" 页, 选择要扩展的私有云。

3. 在 "摘要" 部分中, 单击 "**展开**"。

    ![展开私有云](media/resources-expand-private-cloud.png)

4. 选择是扩展现有群集还是创建新的 vSphere 群集。 进行更改时, 会更新页面上的摘要信息。

    * 若要展开现有群集, 请单击 "**展开现有群集**"。 选择要展开的群集, 然后输入要添加的节点数。 每个群集最多可以有16个节点。
    * 若要添加新群集, 请单击 "新建**群集**"。 输入群集的名称。 选择现有数据中心, 或输入一个名称以创建新数据中心。 选择节点类型。 在创建新的 vSphere 群集时, 可以选择不同的节点类型, 而不是在扩展现有的 vSphere 群集时选择其他节点类型。 选择节点数。 每个新群集必须至少有三个节点。

    ![展开 "私有云-添加节点"](media/resources-expand-private-cloud-add-nodes.png)

5. 单击 "**提交**" 以展开私有云。

## <a name="next-steps"></a>后续步骤

* [在 Azure 上使用 VMware Vm](quickstart-create-vmware-virtual-machine.md)
* 了解有关[私有云](cloudsimple-private-cloud.md)的详细信息