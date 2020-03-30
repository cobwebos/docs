---
title: include 文件
description: include 文件
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: xiaoharper
ms.author: zhanxia
ms.date: 10/18/2019
ms.openlocfilehash: e3cb977871af2e6cd7a59dd48505090dd29e8a76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "75541798"
---
1. 登录到 [Azure 机器学习工作室](https://ml.azure.com)。

1. 将工作区升级到企业版。

    升级后，所有视觉对象界面试验都将转换为设计器中的管道草稿。
    
    > [!NOTE]
    > 不需升级到企业版本即可将视觉对象界面 Web 服务转换为实时终结点。
    
1. 请在工作区的设计器部分查看管道草稿列表。 
    
    转换后的 Web 服务可以通过导航到**端点** > **实时终结点**找到。

1. 选择要打开的管道草案。

    如果在转换过程中出现错误，则会显示错误消息，其中包含用于解决此问题的说明。 

### <a name="known-issues"></a>已知问题

 下面是需要手动解决的已知迁移问题：

- “导入数据”或“导出数据”模块********
        
    如果在试验中有“导入数据”或“导出数据”模块********，则需更新数据源才能使用数据存储。 若要了解如何创建数据存储，请参阅[如何访问 Azure 存储服务中的数据](../articles/machine-learning/how-to-access-data.md)。 你的云存储帐户信息已添加到“导入数据”或“导出数据”模块，******** 方便你使用。 
      