---
title: 将 Symantec AWS 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Symantec AWS 数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 46ad8f6478e632cd57c009700de182993ce01be1
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240867"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>将 Azure Sentinel 连接到 AWS CloudTrail

使用 AWS 连接器将所有 AWS CloudTrail 事件流式传输到 Azure Sentinel。 此连接过程会将 Azure Sentinel 的访问权限委派给 AWS 资源日志, 从而在 AWS CloudTrail 和 Azure Sentinel 之间创建信任关系。 这是在 AWS 上完成的, 方法是创建一个角色, 该角色授予 Azure Sentinel 访问 AWS 日志的权限。

## <a name="prerequisites"></a>先决条件

您必须对 Azure Sentinel 工作区具有写入权限。

> [!NOTE]
> Azure Sentinel 从所有区域收集 CloudTrail 事件。 建议您不要将事件从一个区域流式传输到另一个区域。

## <a name="connect-aws"></a>连接 AWS 


1. 在 Azure Sentinel 中, 选择 "**数据连接器**", 然后在表中选择 " **Amazon Web Services** " 行, 然后在右侧的 "AWS" 窗格中单击 "**打开连接器页面**"。

1. 按照以下步骤使用**配置**下的说明进行操作。
 
1.  在 Amazon Web Services 控制台中, 在 "安全性" 下, 单击 "**标识 & 符合性**", 然后选择**IAM**。

    ![AWS1](./media/connect-aws/aws-1.png)

1.  选择 "**角色**" 并选择 "**创建角色**"。

    ![AWS2](./media/connect-aws/aws-2.png)

1.  请选择**其他 AWS 帐户。** 在 "**帐户 id** " 字段中, 输入可在 Azure Sentinel 门户的 AWS 连接器页中找到的**Microsoft 帐户 id** (**123412341234**)。

    ![AWS3](./media/connect-aws/aws-3.png)

1.  请确保选择 "**需要外部 id** ", 然后输入可在 Azure Sentinel 门户的 AWS 连接器页中找到的外部 Id (工作区 id)。

    ![AWS4](./media/connect-aws/aws-4.png)

1.  在 "**附加权限策略**" 下选择 " **AWSCloudTrailReadOnlyAccess**"。

    ![AWS5](./media/connect-aws/aws-5.png)

1.  输入标记 (可选)。

    ![AWS6](./media/connect-aws/aws-6.png)

1.  然后, 输入**角色名称**并选择 "**创建角色**" 按钮。

    ![AWS7](./media/connect-aws/aws-7.png)

1.  在 "角色" 列表中, 选择你创建的角色。

    ![AWS8](./media/connect-aws/aws-8.png)

1.  复制**角色 ARN**。 在 Azure Sentinel 门户的 "Amazon Web Services 连接器" 屏幕上, 将其粘贴到 "**要添加的角色**" 字段, 然后单击 "**添加**"。

    ![AWS9](./media/connect-aws/aws-9.png)

1. 若要使用 AWS 事件 Log Analytics 中的相关架构, 请搜索**AWSCloudTrail**。



## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 AWS CloudTrail 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何了解[你的数据以及潜在的威胁](quickstart-get-visibility.md)。
- 开始[通过 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

