---
title: 在 Azure 开发测试实验室的实验室中删除实验室或 VM | Microsoft Docs
description: 本文介绍如何在实验室中删除实验室或 VM。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: spelluru
ms.openlocfilehash: 99caf04698226de8daa9cfb8f60662e5cb0f8b49
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39450387"
---
# <a name="delete-a-lab-or-vm-in-a-lab-in-azure-devtest-labs"></a>在 Azure 开发测试实验室的实验室中删除实验室或 VM
本文介绍如何在实验室中删除实验室或 VM。

## <a name="delete-a-lab"></a>删除实验室
从资源组删除开发测试实验室实例时，开发测试实验室服务执行以下操作： 

- 自动删除创建实验室时自动创建的所有资源。 不会删除资源组本身。 如果已手动创建此资源组的任何资源，则该服务不会删除它们。 
- 自动删除实验室中的所有 VM 和与这些 VM 关联的资源组。 在实验室中创建 VM 时，该服务会在单独的资源组中为 VM 创建资源（磁盘、网络接口、公共 IP 地址等）。 但是，如果在这些资源组中手动创建任何其他资源，开发测试实验室服务不会删除这些资源和资源组。 

要删除实验室，请执行以下操作： 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧的菜单中选择“所有资源”，选择“开发测试实验室”服务类型，然后选择实验室。

    ![选择实验室](media\devtest-lab-delete-lab-vm\select-lab.png)
3. 在“开发测试实验室”页上的工具栏中单击“删除”。 

    ![“删除”按钮](media\devtest-lab-delete-lab-vm\delete-button.png)
4. 在“确认”页上，输入实验室的名称，然后选择“删除”。 

    ![确认](media\devtest-lab-delete-lab-vm\confirm-delete.png)
5. 要查看操作状态，请选择“通知”图标（铃铛）。 

    ![通知](media\devtest-lab-delete-lab-vm\delete-status.png)

 
## <a name="delete-a-vm-in-a-lab"></a>删除实验室中的 VM
删除实验室中的 VM 时，会删除在创建实验室时创建的某些资源（并非所有资源）。 不会删除以下资源： 

-   主要资源组中的密钥保管库
-   可用性集、负载均衡器、VM 资源组中的公共 IP 地址。 这些资源由资源组中的多个 VM 共享。 

删除虚拟机、网络接口和与 VM 关联的磁盘。 

要删除实验室中的 VM，请执行以下操作： 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧的菜单中选择“所有资源”，选择“开发测试实验室”服务类型，然后选择实验室。

    ![选择实验室](media\devtest-lab-delete-lab-vm\select-lab.png)
3. 在 VM 列表中为 VM 选择“...(省略号)”，然后选择“删除”。 

    ![在菜单中删除 VM](media\devtest-lab-delete-lab-vm\delete-vm-menu-in-list.png)
4. 在“确认”对话框中，选择“确定”。 
5. 要查看操作状态，请选择“通知”图标（铃铛）。 

要从“虚拟机页”删除 VM，请选择下图中显示的工具栏中的“删除”：

![从 VM 页中删除 VM](media\devtest-lab-delete-lab-vm\delete-from-vm-page.png) 


## <a name="next-steps"></a>后续步骤
如果要创建实验室，请参阅以下文章： 

- [创建实验室](devtest-lab-create-lab.md)
- [将 VM 添加到实验室](devtest-lab-add-vm.md)