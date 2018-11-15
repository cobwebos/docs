---
title: 在本教程中，你学习 璹綷 Azure 堆栈优惠 |Microsoft 文档
description: 本教程介绍如何创建 Azure Stack 服务的新订阅，并通过创建测试虚拟机来测试套餐。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/13/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 35b3e6d59cdb1edc59aa211954b8a69a3ce51bc5
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2018
ms.locfileid: "51632199"
---
# <a name="tutorial-create-and-test-a-subscription"></a>教程：创建和测试订阅

本教程介绍如何创建包含套餐的订阅，然后对其进行测试。 测试时，登录到云管理员在 Azure Stack 用户门户订阅产品/服务，然后创建虚拟机。

> [!TIP]
> 进行更多更高级的评估体验，你可以[为特定用户创建订阅](../azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)然后以该用户门户中的用户身份登录。 

本教程介绍如何订阅 Azure Stack 套餐。

要学习的知识：

> [!div class="checklist"]
> * 订阅产品 
> * 测试产品/服务

## <a name="subscribe-to-an-offer"></a>订阅产品

若要订阅产品/服务以用户身份，您登录到 Azure Stack 用户门户来发现 Azure Stack 操作员提供的服务。

1. 登录到用户门户并选择**获取订阅**。

   ![获取订阅](media/azure-stack-subscribe-services/get-subscription.png)

2. 在“显示名称”字段中，键入订阅的名称。 然后选择**产品/服务**选择一个可用的产品/服务中**选择产品/服务**部分。 然后选择“创建”。

   ![创建产品](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > 现在，必须刷新用户门户以开始使用该订阅。

3. 若要查看创建的订阅，请选择**所有服务**。 然后，在“常规”类别下选择“订阅”，然后选择新订阅。 订阅套餐之后，请刷新门户，以查看新服务是否已包含为新订阅的一部分。 在本示例中，我们已添加**虚拟机**。

   ![查看订阅](media/azure-stack-subscribe-services/view-subscription.png)

## <a name="test-the-offer"></a>测试产品/服务

登录到用户门户，可以通过设置使用新的订阅功能的虚拟机中测试产品/服务。 

> [!NOTE]
> 此项测试需要用到事先已添加到 Azure Stack 市场的 Windows Server 2016 Datacenter VM。 

1. 登录到用户门户。

2. 在用户门户中，选择**虚拟机**，然后**添加**，然后**Windows Server 2016 Datacenter**，然后单击**创建**。

3. 在中**基础知识**部分中，键入**名称**，**用户名**，以及**密码**，选择**订阅**，创建**资源组**（或选择一个现有），然后选择**确定**。

4. 在中**选择的大小**部分中，选择**A1 标准**，然后单击**选择**。  

5. 在中**设置**边栏选项卡上，接受默认值，然后选择**确定**。

6. 在“摘要”部分，单击“确定”创建虚拟机。  

7. 若要查看新的虚拟机，请选择**虚拟机**，然后搜索新的虚拟机，并单击其名称。

    ![所有资源](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> 虚拟机部署需要几分钟时间才能完成。


## <a name="next-steps"></a>后续步骤

本教程已介绍如何执行以下操作：

> [!div class="checklist"]
> * 订阅产品 
> * 测试产品/服务


> [!div class="nextstepaction"]
> [从社区模板创建 VM](azure-stack-create-vm-template.md)