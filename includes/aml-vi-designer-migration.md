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
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2019
ms.locfileid: "75541798"
---
1. 登录到 [Azure 机器学习工作室](https://ml.azure.com)。

1. 将工作区升级到 Enterprise edition。

    升级后，所有视觉对象界面试验都将转换为设计器中的管道草稿。
    
    > [!NOTE]
    > 不需要升级到 Enterprise edition 即可将视觉对象界面 web 服务转换为实时终结点。
    
1. 请在工作区的 "设计器" 部分查看管道草稿列表。 
    
    可以通过导航到**终结点** > **实时终结点**来找到转换后的 web 服务。

1. 选择要打开的管道草案。

    如果在转换过程中出现错误，将显示一条错误消息，其中包含用于解决此问题的说明。 

### <a name="known-issues"></a>已知问题

 下面是需要手动解决的已知迁移问题：

- **导入数据**或**导出数据**模块
        
    如果在试验中有 "**导入数据**" 或 "**导出数据**" 模块，则需要更新数据源以使用数据存储。 若要了解如何创建数据存储，请参阅[如何访问 Azure 存储服务中的数据](../articles/machine-learning/how-to-access-data.md)。 为了方便起见，已在 "**导入数据**" 或 "**导出数据**" 模块的注释中添加了你的云存储帐户信息。 
      