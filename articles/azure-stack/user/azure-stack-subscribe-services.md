---
title: 在本教程中，你学习 璹綷 Azure 堆栈优惠 |Microsoft 文档
description: 本教程介绍如何创建 Azure Stack 服务的新订阅，并通过创建测试虚拟机来测试产品/服务。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 96b82ede71e7957105dce25096d7873ee876211f
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238365"
---
# <a name="tutorial-create-and-test-a-subscription"></a>教程：创建和测试订阅
本教程介绍如何创建包含产品/服务的订阅，然后对其进行测试。 为了使测试，你将登录到 Azure 堆栈用户门户的云管理员联系，订阅产品，，然后创建虚拟机。

> [!TIP]
> 若要获得更高级的评估体验，可[为特定用户创建订阅](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm#create-a-subscription-as-a-cloud-operator)，然后在用户门户中以该用户的身份登录。 

本教程演示如何订阅 Azure 堆栈产品。

要学习的知识：

> [!div class="checklist"]
> * 订阅产品 
> * 测试产品/服务

## <a name="subscribe-to-an-offer"></a>订阅产品
若要以用户身份订阅产品/服务，需要登录到 Azure Stack 用户门户，以发现 Azure Stack 操作员提供的服务。

1. 登录到用户门户并单击**获取订阅**。

   ![获取订阅](media/azure-stack-subscribe-services/get-subscription.png)

2. 在“显示名称”字段中，键入订阅的名称。 然后，单击“产品/服务”，在“选择产品/服务”部分中选择某个可用产品/服务，然后单击“创建”。

   ![创建产品](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > 现在，必须刷新用户门户以开始使用该订阅。

3. 若要查看刚刚创建的订阅，请依次单击“更多服务”、“订阅”和新的订阅。 订阅产品/服务之后，请刷新门户，以查看新服务是否已包含为新订阅的一部分。 在本示例中，我们已添加**虚拟机**。

   ![查看订阅](media/azure-stack-subscribe-services/view-subscription.png)


## <a name="test-the-offer"></a>测试产品/服务
在登录到用户门户，你可以通过设置使用新的订阅功能的虚拟机来测试产品/服务。 

> [!NOTE]
> 此测试需要到 Azure 堆栈 marketplace 第一次添加一个 Windows Server 2016 数据中心虚拟机。 

1. 登录到用户门户。

2. 在用户门户中，单击“虚拟机” > “添加” > “Windows Server 2016 Datacenter”，然后单击“创建”。

3. 在“基本信息”部分，输入“名称”、“用户名”和“密码”，选择“订阅”，创建一个**资源组**，然后单击“确定”。

4. 在“选择大小”部分单击“A1 标准”，然后单击“选择”。  

5. 在“设置”边栏选项卡中接受默认值，然后单击“确定”。

6. 在“摘要”部分，单击“确定”创建虚拟机。  

7. 若要查看新虚拟机，请单击“虚拟机”，然后搜索新虚拟机并单击其名称。

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