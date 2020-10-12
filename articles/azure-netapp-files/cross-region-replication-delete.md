---
title: 删除 Azure NetApp 文件跨区域复制的复制 |Microsoft Docs
description: 描述如何删除源卷和目标卷之间不再需要的复制连接。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: 0904ac36a9453e51dbb1efc50eee2b9bf3c669c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708554"
---
# <a name="delete-replications"></a>删除副本

可以通过删除卷复制来终止源卷和目标卷之间的复制连接。 可以从源或目标卷执行删除操作。 删除操作仅删除复制的授权;它不会删除源或目标卷。 

## <a name="steps"></a>步骤

1. 若要删除卷复制，请从源或目标卷中选择 " **复制** "。  

2. 单击 **“删除”** 。    

3. 键入 Yes 并单击 **"** **删除**"，确认删除。   

    ![删除复制](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="next-steps"></a>后续步骤  

* [跨区域复制](cross-region-replication-introduction.md)
* [使用跨区域复制的要求和注意事项](cross-region-replication-requirements-considerations.md)
* [显示复制关系的运行状况](cross-region-replication-display-health-status.md)
* [跨区域复制故障排除](troubleshoot-cross-region-replication.md)

