---
title: 将 AWS 云跟踪连接到 Azure 哨兵 |微软文档
description: 了解如何将 AWS 云跟踪数据连接到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 5cbef1f31ea7088d4fab4888f5630af1b765a910
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588648"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>将 Azure 哨兵连接到 AWS 云跟踪

使用 AWS 连接器将所有 AWS 云跟踪事件流式传输到 Azure 哨兵中。 此连接进程将 Azure Sentinel 的访问权限委派到 AWS 资源日志，从而在 AWS CloudTrail 和 Azure Sentinel 之间创建信任关系。 这是通过在 AWS 上创建一个角色来实现的，该角色授予 Azure Sentinel 访问 AWS 日志的权限。

## <a name="prerequisites"></a>先决条件

您必须在 Azure Sentinel 工作区上具有写入权限。

> [!NOTE]
> Azure 哨兵从所有区域收集云跟踪事件。 建议您不要将事件从一个区域流式传输到另一个区域。

## <a name="connect-aws"></a>连接 AWS 


1. 在 Azure 哨兵中，选择 **"数据连接器**"，然后在表格中和右侧的 AWS 窗格中选择**Amazon Web 服务**行，单击 **"打开连接器"页**。

1. 使用以下步骤按照**配置**下的说明进行操作。
 
1.  在您的亚马逊 Web 服务控制台中 **，在"安全、身份&合规性**"下，选择**IAM**。

    ![AWS1](./media/connect-aws/aws-1.png)

1.  选择**角色**并选择 **"创建角色**"。

    ![AWS2](./media/connect-aws/aws-2.png)

1.  选择**另一个 AWS 账户。** 在 **"帐户 ID"** 字段中，输入可在 Azure Sentinel 门户中的 AWS 连接器页面中找到的**Microsoft 帐户 ID** **（123412341234）。**

    ![AWS3](./media/connect-aws/aws-3.png)

1.  确保选择了 **"需要外部 ID"，** 然后输入可在 Azure Sentinel 门户中的 AWS 连接器页面中找到的外部 ID（工作区 ID）。

    ![AWS4](./media/connect-aws/aws-4.png)

1.  在**附加权限策略**下选择**AWSCloudTrailRead 仅访问**。

    ![AWS5](./media/connect-aws/aws-5.png)

1.  输入标签（可选）。

    ![AWS6](./media/connect-aws/aws-6.png)

1.  然后，输入**角色名称**并选择"**创建角色"** 按钮。

    ![AWS7](./media/connect-aws/aws-7.png)

1.  在"角色"列表中，选择您创建的角色。

    ![AWS8](./media/connect-aws/aws-8.png)

1.  复制**角色 ARN**。 在 Azure Sentinel 门户中，在 Amazon Web 服务连接器屏幕中，将其粘贴到 **"角色"中以添加**字段，然后单击"**添加**"。

    ![AWS9](./media/connect-aws/aws-9.png)

1. 要在日志分析中使用相关的架构进行 AWS 事件，请搜索**AWSCloudTrail**。



## <a name="next-steps"></a>后续步骤
在本文中，您学习了如何将 AWS 云跟踪连接到 Azure 哨兵。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。

