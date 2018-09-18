---
title: 在 Azure Stack 中添加公共 IP 地址 |Microsoft Docs
description: 了解如何将多个公共 IP 地址添加到 Azure Stack。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeffgilb
ms.reviewer: scottnap
ms.openlocfilehash: b401139d417674cf58d2db264b442d7588cc34ba
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2018
ms.locfileid: "45986411"
---
# <a name="add-public-ip-addresses"></a>添加公共 IP 地址
*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*  

了解如何将多个公共 IP 地址添加到 Azure Stack。  在本文中，我们引用外部地址作为公共 IP 地址，但在 Azure Stack 中这指到 IP 地址块添加到外部网络，请参阅。  该外部网络是否是公共 Internet 可路由或位于 Intranet 上并使用专用地址空间实际上并不重要的这篇文章的目的。  步骤是相同的。 

## <a name="add-a-public-ip-address-pool"></a>添加公共 IP 地址池
可以在 Azure Stack 系统的初始部署之后的任何时间向你的 Azure Stack 系统中添加公共 IP 地址。 请了解如何[查看公共 IP 地址使用情况](azure-stack-viewing-public-ip-address-consumption.md)若要查看哪些当前使用情况和公共 IP 地址可用性是在 Azure Stack 上。

在高级别中，将新的公共 IP 地址块添加到 Azure Stack 的过程如下所示：

 ![添加 IP 流](media/azure-stack-add-ips/flow.PNG)

## <a name="obtain-the-address-block-from-your-provider"></a>从您的提供程序获取地址块
将需要执行的第一个操作是获取你想要添加到 Azure Stack 的地址块。  具体取决于从何处获取你地址块，将需要考虑的提前期是什么，并对在其中使用 Azure Stack 中的公共 IP 地址的速率对此进行管理。  

> [!IMPORTANT]
> Azure Stack 将接受任何提供，只要它是有效的地址块并不与 Azure Stack 中的现有地址范围重叠的地址块。  请确保你获取路由，并且非重叠与 Azure Stack 连接到外部网络的有效地址块。  一旦将范围添加到 Azure Stack，则不能将其删除。

## <a name="add-the-ip-address-range-to-azure-stack"></a>将 IP 地址范围添加到 Azure Stack

1. 在 Internet 浏览器，导航到管理门户仪表板。  对于此示例中，我们将使用 https://adminportal.local.azurestack.external。  
2.  以云操作员身份登录到 Azure Stack 管理门户。
3.  默认仪表板 – 上找到区域管理列表并选择你想要管理，对于此示例中，本地的区域。
4.  查找资源提供程序磁贴，然后单击对网络资源提供程序。
5.  单击公共 IP 池使用情况磁贴。
6.  单击添加 IP 池按钮。
7.  提供 IP 池的名称。  选择是只是让你可以轻松地识别 IP 池，因此您可以对其任意命名的名称。  很好的做法，以使名称相同的地址范围，但不是必需。
8.   输入你想要在 CIDR 表示法中添加地址块。  例如： 192.168.203.0/24
9.  当你提供的起始 IP 地址的地址范围 （CIDR 块） 字段中的有效 CIDR 范围时，，系统将自动填充结束 IP 地址和可用的 IP 地址字段。  它们是只读的因此您无法更改它们而无需修改地址范围字段中的值自动生成。
10. 之后，查看边栏选项卡上的信息并确认所有内容正确，单击确定以提交更改，然后将该地址范围添加到 Azure Stack。

## <a name="update-the-acls-on-your-top-of-rack-switches"></a>更新在机架顶部交换机上的 Acl
您需要执行的操作启用新添加的 IP 范围，若要运行的最后一步是更新访问控制列表 (Acl) 顶部柜顶式 (ToR) 交换机上。  ToR 交换机已锁定的方式从 Azure Stack 外部的新添加的 IP 范围到连接，以及将正常新范围上的 Acl 添加到的交换机上的 Acl。  

需要联系 OEM 并与他们合作更新 ToR 交换机上的 Acl。  它们具有必要的工具来执行此操作中受支持的方式。


## <a name="next-steps"></a>后续步骤 
[查看缩放单位节点操作](azure-stack-node-actions.md) 
