---
title: 将 Symantec AWS 数据连接到 Azure Sentinel 预览版 |Microsoft Docs
description: 了解如何将 Symantec AWS 数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 214269bc5c854aa4d3bfd508b0adb5a53ec096df
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673968"
---
# <a name="connect-azure-sentinel-to-aws"></a>连接 AWS 到 Azure 的 Sentinel

AWS 连接器用于所有在 AWS CloudTrail 事件流式传输到 Azure Sentinel。 该连接过程委托访问 Azure Sentinel 针对 AWS 资源日志，请创建 AWS CloudTrail 和 Azure Sentinel 之间的信任关系。 实现这一点在 AWS 通过创建到 Azure Sentinel 访问 AWS 日志授予的权限的角色。

## <a name="prerequisites"></a>先决条件

在 Azure Sentinel 工作区上，必须具有写入权限。

## <a name="connect-aws"></a>连接 AWS 
 
1.  在 Amazon Web Services 控制台中下,**安全、 标识和符合性**，单击**IAM**。

    ![AWS1](./media/connect-aws/aws-1.png)

2.  选择**角色**然后单击**创建角色**。

    ![AWS2](./media/connect-aws/aws-2.png)

3.  选择**另一个 AWS 帐户。** 在中**帐户 ID**字段中，输入**的 Microsoft 帐户 ID** (**123412341234**) 可在 Azure Sentinel 门户的 AWS 连接器页中找到。

    ![AWS3](./media/connect-aws/aws-3.png)

4.  请确保**需要外部 ID**处于选中状态，然后输入可在 Azure Sentinel 门户的 AWS 连接器页中找到外部 ID (工作区 ID)。

    ![AWS4](./media/connect-aws/aws-4.png)

5.  下**附加权限策略**选择**AWSCloudTrailReadOnlyAccess**。

    ![AWS5](./media/connect-aws/aws-5.png)

6.  输入标记 （可选）。

    ![AWS6](./media/connect-aws/aws-6.png)

7.  然后，输入**角色名称**然后单击**创建角色**按钮。

    ![AWS7](./media/connect-aws/aws-7.png)

8.  在角色列表中，选择你创建的角色。

    ![AWS8](./media/connect-aws/aws-8.png)

9.  复制**角色 ARN**将其粘贴到**角色添加**Sentinel 在 Azure 门户中的字段。

    ![AWS9](./media/connect-aws/aws-9.png)

10. 若要使用 Log Analytics 中的 AWS 事件相关的架构，搜索**AWSCloudTrail**。



## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将 AWS CloudTrail 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[来了解一下你的数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[检测威胁 Azure Sentinel](tutorial-detect-threats.md)。

