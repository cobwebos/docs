---
title: "在本教程中，你学习 璹綷 Azure 堆栈优惠 |Microsoft 文档"
description: "本教程演示如何创建 Azure 堆栈服务的新订阅和测试创建测试虚拟机的产品/服务。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: d2adda5613b9a10bc3314045b9d68b0f3196f19a
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-create-and-test-a-subscription"></a>教程： 创建和测试订阅
本教程演示如何创建包含提议的订阅，然后测试它。 为了使测试，你将登录到[用户门户](https://portal.local.azurestack.external)作为云管理员，订阅到产品/服务，然后创建虚拟机。

> [!TIP]
> 对于多更高级的计算体验，您可以[为特定用户创建订阅](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm#create-a-subscription-as-a-cloud-operator)，然后在用户门户中该用户的登录。 

本教程演示如何订阅中创建产品[以前一教程](asdk-offer-services.md)。

要学习的知识：

> [!div class="checklist"]
> * 订阅产品 
> * 测试产品

## <a name="subscribe-to-an-offer"></a>订阅产品
若要订阅服务关联的用户，你需要登录到 Azure 堆栈用户门户，以便发现向提供了由 Azure 堆栈运营商的服务。

1. 登录到[用户门户](https://portal.local.azurestack.external)单击**获取订阅**。

   ![获取订阅](media/asdk-subscribe-services/get-subscription.png)

2. 在**显示名称**字段中，键入你的订阅的名称。 然后，单击**提供**选择其中一种中可用的优惠**选择提议**节，再单击**创建**。

   ![创建产品](media/asdk-subscribe-services/create-subscription.png)

   > [!TIP]
   > 现在，您必须刷新用户门户，以便开始使用你的订阅。

3. 若要查看你刚刚创建的订阅，请单击**更多的服务**，单击**订阅**，然后单击你的新订阅。 在订阅产品/服务后，刷新门户以查看新的服务是否已的新订阅的一部分。 在此示例中，**虚拟机**已添加。

   ![查看订阅](media/asdk-subscribe-services/view-subscription.png)


## <a name="test-the-offer"></a>测试产品
在登录到[用户门户](https://portal.local.azurestack.external)，你可以通过设置使用新的订阅功能的虚拟机来测试产品/服务。 

> [!NOTE]
> 此测试需要已添加到 Azure 堆栈 marketplace 中的 Windows Server 2016 数据中心 VM 映像[以前一教程](asdk-marketplace-item.md)。 

1. 登录到[用户门户](https://portal.local.azurestack.external)。

2. 在用户门户中，单击**虚拟机** > **添加** > **Windows Server 2016 Datacenter**，然后单击**创建**.

3. 在“基本信息”部分，输入“名称”、“用户名”和“密码”，选择“订阅”，创建一个**资源组**，然后单击“确定”。

4. 在“选择大小”部分单击“A1 标准”，然后单击“选择”。  

5. 在设置边栏选项卡，接受默认值，然后单击**确定**。

6. 在“摘要”部分，单击“确定”创建虚拟机。  

7. 若要查看新的虚拟机，单击**虚拟机**，对于新的虚拟机，然后搜索并单击其名称。

    ![所有资源](media/asdk-subscribe-services/view-vm.png)

> [!NOTE]
> 虚拟机部署将需要几分钟才能完成。


## <a name="next-steps"></a>后续步骤

本教程已介绍如何执行以下操作：

> [!div class="checklist"]
> * 订阅产品 
> * 测试产品

前进到下一步的教程，若要了解如何使用模板创建 VM。

> [!div class="nextstepaction"]
> [从模板创建虚拟机](asdk-create-vm-template.md)