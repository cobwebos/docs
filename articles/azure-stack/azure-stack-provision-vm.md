---
title: "在 Azure 堆栈中创建测试虚拟机 |Microsoft 文档"
description: "了解如何设置测试 Azure 作为云操作员的堆栈中的虚拟机。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: c86646e1-a12e-493f-b396-f17bfacd60c2
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: erikje
ms.openlocfilehash: 233cf4df53af6a49e5fe4c5d51e112d8196a7530
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-test-virtual-machine-in-azure-stack"></a>在 Azure 堆栈中创建测试虚拟机

*适用范围： Azure 堆栈开发工具包*

作为 Azure 堆栈运算符，你可以创建测试虚拟机，以验证你[Azure 堆栈](azure-stack-poc.md)开发人员工具包部署。

> [!NOTE]
> 你可以设置虚拟机之前，你必须[将 Windows Server 2016 评估版映像添加到 Azure 堆栈 marketplace](azure-stack-add-default-image.md)。
> 
> 

## <a name="create-a-virtual-machine"></a>创建虚拟机
1. 在 Azure 堆栈开发工具包主机上，[登录](azure-stack-connect-azure-stack.md)到管理员门户 (`https://adminportal.local.azurestack.external`)，然后单击**新建** > **计算** > **评估版的 Windows Server 2016 数据中心** > **创建**。  
2. 在**基础知识**边栏选项卡中，键入一个**名称**，**用户名**，和**密码**。 选择**订阅**。 创建**资源组**，或选择一个现有，然后单击**确定**。  
3. 在**选择大小**边栏选项卡，单击**A1 标准**，然后单击**选择**。  
4. 在**设置**边栏选项卡中，接受默认值，然后单击**确定**
5. 在**摘要**边栏选项卡，单击**确定**创建虚拟机。  
6. 若要查看新的虚拟机，单击**的所有资源**，然后搜索虚拟机并单击其名称。
    ![](media/azure-stack-provision-vm/image06.png)


## <a name="next-steps"></a>后续步骤
[在 Azure 堆栈中使用的管理员和用户门户](azure-stack-manage-portals.md)
