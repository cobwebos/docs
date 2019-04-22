---
title: include 文件
description: include 文件
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/16/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b84b0a8e09bf739ce62dee167ff751b491765c66
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684661"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>为事件处理程序主机创建存储帐户
事件处理程序主机是一个智能代理，它通过管理持久性检查点和并行接收操作，来简化从事件中心接收事件的过程。 对于检查点，事件处理程序主机需要一个存储帐户。 以下示例演示如何创建存储帐户，以及如何获取其密钥以进行访问：

1. 在 Azure 门户中，选择屏幕左上角的“创建资源”。

2. 选择“存储”，然后选择“存储帐户 - Blob、文件、表、队列”。
   
    ![选择存储帐户](./media/event-hubs-create-storage/create-storage1.png)

3. 在“创建存储帐户”页中执行以下步骤： 

   1. 输入存储帐户的名称。 
   2. 选择包含事件中心的 Azure 订阅。
   3. 选择包含事件中心的资源组。
   4. 选择可在其中创建资源的位置。 
   5. 然后单击“查看 + 创建”。
   
      ![创建存储帐户 - 页面](./media/event-hubs-create-storage/create-storage2.png)

4. 在“查看 + 创建”页上查看值，然后选择“创建”。 

    ![查看存储帐户设置，然后执行创建操作](./media/event-hubs-create-storage/review-create-storage-account.png)
5. 在看到后**部署成功**消息中，选择**转到资源**页的顶部。 还可以通过从资源列表中选择存储帐户来启动“存储帐户”页。  

    ![从部署中选择存储帐户](./media/event-hubs-create-storage/select-storage-deployment.png) 
7. 在“概要”窗口中选择“Blob”。 

    ![选择 Blob 服务](./media/event-hubs-create-storage/select-blobs-service.png)
1. 选择顶部的“+ 容器”，为容器输入**名称**，然后选择“确定”。 

    ![创建 Blob 容器](./media/event-hubs-create-storage/create-blob-container.png)
1. 在左侧菜单中选择”访问密钥”，然后复制 **key1** 的值。 

    将以下值保存到记事本或其他某个临时位置。
    - 存储帐户的名称
    - 存储帐户的访问密钥
    - 容器的名称
