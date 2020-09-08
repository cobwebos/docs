---
title: 在组织外共享（Azure 门户）- Azure Data Share 快速入门
description: 在此快速入门中了解如何使用 Azure Data Share 与客户和合作伙伴共享数据。
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: quickstart
ms.date: 08/19/2020
ms.openlocfilehash: 5ceaf949df88468b2239bd901f639ba6096b0d5f
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269635"
---
# <a name="quickstart-share-data-using-azure-data-share-in-the-azure-portal"></a>快速入门：在 Azure 门户中使用 Azure Data Share 共享数据

在此快速入门中，你将了解如何使用 Azure 门户设置新的 Azure Data Share。

## <a name="prerequisites"></a>必备条件

Azure 订阅：如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。


## <a name="create-a-data-share-account"></a>创建 Data Share 帐户

在 Azure 资源组中创建 Azure Data Share 资源。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 选择门户左上角的“创建资源”按钮 (+)。

1. 搜索“Data Share”。**

1. 选择“Data Share”，然后选择“创建” 。

1. 根据以下信息填写 Azure Data Share 资源的基本详细信息。 

   **设置** | **建议的值** | **字段说明**
   |---|---|---|
   | 订阅 | 你的订阅 | 选择要用于 Data Share 帐户的 Azure 订阅。|
   | 资源组 | *test-resource-group* | 使用现有资源组或创建新资源组。 |
   | 位置 | *美国东部 2* | 选择 Data Share 帐户的区域。
   | 名称 | *datashareaccount* | 指定 Data Share 帐户的名称。 |

1. 选择“审阅 + 创建”，然后选择“创建”来预配数据共享帐户 。 预配新的 Data Share 帐户通常需要大约 2 分钟或更少的时间。

1. 部署完成后，选择“转到资源”。

## <a name="create-a-share"></a>创建共享

1. 导航到 Data Share 概览页。

   ![共享数据](./media/share-receive-data.png "共享数据") 

1. 选择“开始共享数据”。

1. 选择“创建”。

1. 填充共享的详细信息。 指定名称、共享类型、共享内容说明以及使用条款（可选）。 

   ![EnterShareDetails](./media/enter-share-details.png "输入共享详细信息") 

1. 选择“继续”。

1. 若要向共享添加数据集，请选择“添加数据集”。 

   ![向共享添加数据集](./media/datasets.png "数据集")

1. 选择要添加的数据集类型。 你将会看到一个不同的数据集类型列表，具体取决于你在上一步中选择的共享类型（快照或就地）。 如果从 Azure SQL 数据库或 Azure SQL 数据仓库进行共享，系统将提示你输入一些 SQL 凭据。 使用你在先决条件部分中创建的用户进行身份验证。

   ![AddDatasets](./media/add-datasets.png "添加数据集")    

1. 导航到要共享的对象，选择“添加数据集”。 

   ![SelectDatasets](./media/select-datasets.png "选择数据集")    

1. 在“接收者”选项卡中，选择“+ 添加接收者”，输入数据使用者的电子邮件地址。

   ![AddRecipients](./media/add-recipient.png "添加收件人") 

1. 选择“继续”。

1. 如果已选择了快照共享类型，则可以配置快照计划来向数据使用者提供数据更新。 

   ![EnableSnapshots](./media/enable-snapshots.png "启用快照") 

1. 选择开始时间和重复周期间隔。 

1. 选择“继续”。

1. 在“查看 + 创建”选项卡中查看包内容、设置、接收者和同步设置。 选择“创建”。

你的 Azure Data Share 现已创建，Data Share 的接收者现已准备好接受你的邀请。

## <a name="clean-up-resources"></a>清理资源

当不再需要该资源时，转到“数据共享概述”页面并选择“删除”将其删除 。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何创建 Azure Data Share。 若要了解数据使用者如何接受和接收数据共享，请继续学习[接受和接收数据](subscribe-to-data-share.md)教程。 
