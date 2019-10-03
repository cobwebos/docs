---
title: include 文件
description: include 文件
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ace42278269ff6af31902dbecead81329815af12
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173186"
---
## <a name="create-a-topic-using-the-azure-portal"></a>使用 Azure 门户创建主题
1. 在“服务总线命名空间”  页面上，选择左侧菜单中的“主题”  。
2. 在工具栏中选择“+ 主题”。  
4. 输入主题名称  。 将其他选项保留默认值。
5. 选择“创建”  。

    ![创建主题](./media/service-bus-create-topics-subscriptions-portal/create-topic.png)

## <a name="create-subscriptions-to-the-topic"></a>创建对主题的订阅
1. 选择在上一部分创建的主题  。 
    
    ![选择主题](./media/service-bus-create-topics-subscriptions-portal/select-topic.png)
2. 在“服务总线主题”  页面上，从左侧菜单中选择“订阅”  ，然后工具栏上选择“+ 订阅”  。 
    
    ![添加订阅按钮](./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png)
3. 在“创建订阅”  页面上，输入 **S1** 作为订阅名称  ，然后选择“创建”  。 

    ![创建订阅页面](./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png)
4. 重复上述步骤两次，创建名为 **S2** 和 **S3** 的订阅。