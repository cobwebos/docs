---
title: 将 Google Cloud Platform 帐户连接到 Azure 成本管理 | Microsoft Docs
description: 连接 Google Cloud Platform 帐户以便在成本管理报表中查看成本和用量数据。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: b7141d028c496b75b061c17aa19ad6c73c0588c0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32164408"
---
# <a name="connect-a-google-cloud-platform-account"></a>连接 Google Cloud Platform 帐户

可将现有的 Google Cloud Platform 帐户连接到 Azure 成本管理。 将帐户连接到成本管理后，成本管理报表中会提供成本和用量数据。 本文帮助你配置和连接 Google 帐户与成本管理。

## <a name="collect-project-information"></a>收集项目信息

首先收集有关项目的信息。

1. 通过 [https://console.cloud.google.com](https://console.cloud.google.com) 登录到 Google Cloud Platform 控制台。
2. 查看要加入成本管理的项目信息，并记下“项目名称”和“项目 ID”。 请保留此信息，以便在后面的步骤中使用。  
    ![Google Cloud Platform 控制台](./media/connect-google-account/gcp-console01.png)
3. 如果计费未启用并链接到项目，请创建计费帐户。 有关详细信息，请参阅[创建新的计费帐户](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create\_a\_new\_billing\_account)。

## <a name="enable-storage-bucket-billing-export"></a>启用存储桶计费导出

成本管理从存储桶中检索 Google 计费数据。 请保留好“存储桶名称”和“报表前缀”信息，以便稍后在成本管理注册期间使用。

使用 Google Cloud Storage 存储用量报表会产生少量的费用。 有关详细信息，请参阅[云存储定价](https://cloud.google.com/storage/pricing)。

1. 如果尚未启用在文件中导出计费，请遵照[如何启用在文件中导出计费](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file)中的说明。 可以使用 JSON 或 CSV 计费导出格式。
2. 否则，请在 Google Cloud Platform 控制台中，导航到“计费” > “计费导出”。 记下计费的“存储桶名称”和“报表前缀”。  
    ![计费导出](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>启用 Google Cloud Platform API

若要收集用量和资产信息，成本管理需要启用以下 Google Cloud Platform API：

- BigQuery API
- Google Cloud SQL
- Google Cloud Datastore API
- Google Cloud Storage
- Google Cloud Storage JSON API
- Google Compute Engine API

### <a name="enable-or-verify-apis"></a>启用或验证 API

1. 在 Google Cloud Platform 控制台中，选择要注册到成本管理的项目。
2. 导航到“API 和服务” > “库”。
3. 使用搜索查找前面列出的每个 API。
4. 对于每个 API，检查是否显示了“已启用 API”。 否则，请单击“启用”。

## <a name="add-a-google-cloud-account-to-cost-management"></a>将 Google Cloud 帐户添加到成本管理

1. 通过 Azure 门户打开 Cloudyn 门户，或导航到 [https://azure.cloudyn.com](https://azure.cloudyn.com/) 并进行登录。
2. 单击“设置”（齿轮符号），然后选择“云帐户”。
3. 在“帐户管理”中选择“Google 帐户”选项卡，然后单击“新增 +”。
4. 在“Google 帐户名称”中，输入计费帐户的电子邮件地址，然后单击“下一步”。
5. 在 Google 身份验证对话框中，选择或输入 Google 帐户，然后选择“允许”cloudyn.com 访问你的帐户。
6. 添加前面记下的请求项目信息。 这些包括“项目 ID”、“项目名称”、“计费存储桶名称”和“计费文件报表前缀”。然后单击“保存”。  
    ![添加 Google 项目](./media/connect-google-account/add-project.png)

你的 Google 帐户将显示在帐户列表中，并且应指示“已经过身份验证”。 在帐户下面，应会显示附带绿色勾选标记的 Google 项目名称和 ID。 帐户状态应指示“已完成”。

在几个小时内，成本管理报表会显示 Google 成本和用量信息。

## <a name="next-steps"></a>后续步骤

- 若要详细了解 Azure 成本管理，请继续学习成本管理的[查看使用量和成本](./tutorial-review-usage.md)教程。
