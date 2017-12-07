---
title: "使用 VMware vCenter 标记对 VM 进行分组 | Microsoft 文档"
description: "介绍如何在使用 Azure Migrate 服务运行评估之前创建组。"
services: migration-planner
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5c279804-aa30-4946-a222-6b77c7aac508
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: db33e31cdef143aa70809442457e447446a1681a
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2017
---
# <a name="group-vms-with-vcenter-tagging"></a>使用 vCenter 标记对 VM 进行分组

本文介绍如何使用 VMware vCenter 中的标记为 [Azure Migrate](migrate-overview.md) 评估创建一组计算机。 指定你想要在创建组时使用的标记类别。 

## <a name="set-up-tagging"></a>设置标记

在 Azure Migrate 部署期间，本地 Azure Migrate VM 会发现在 vCenter 服务器托管的 ESXi 主机上正在运行的计算机。 你需要在发现过程之前设置 vCenter 标记。

1. 在 VMware vSphere Web 客户端中，导航到 vCenter 服务器。
2. 单击“标记”，以查看任何当前标记。
3. 若要标记 VM，请选择“相关对象” > “虚拟机”，然后选择要标记的 VM。
4. 在“摘要” > “标记”中，单击“分配”。 
5. 单击“新建标记”，并指定标记名称和描述。
6. 若要创建标记的类别，请在下拉列表中选择“新建类别”。
7. 指定类别名称和描述以及多重性。 然后单击“确定”。

    ![VM 标记](./media/how-to-tag-v-center/vm-tag.png)

## <a name="use-tagging-to-create-groups"></a>使用标记创建组

1. 按照 [VMware 评估教程](tutorial-assessment-vmware.md#run-the-collector-to-discover-vms)所述设置本地计算机的发现。
2. 在“分组的标记类别”中，选择评估组应基于的 vCenter 标记类别。 Azure Migrate 会自动创建一个所选类别的组。

    

## <a name="next-steps"></a>后续步骤

[设置 VMware VM 评估](tutorial-assessment-vmware.md)。
