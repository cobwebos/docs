---
title: 教程 - 将 Azure Active Directory 日志存档到 Azure 存储帐户（预览版）| Microsoft Docs
description: 了解如何设置 Azure 诊断来将 Azure Active Directory 日志推送到存储帐户（预览版）
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: ea26da91e2a0b88e8afa54a4210b7a2365e4949b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240155"
---
# <a name="tutorial-archive-azure-active-directory-logs-to-an-azure-storage-account-preview"></a>教程： 将 Azure Active Directory 日志存档到 Azure 存储帐户（预览版）

在本教程中，你将学习如何设置 Azure Monitor 诊断设置来将 Azure Active Directory 日志路由到 Azure 存储帐户。

## <a name="prerequisites"></a>先决条件 

需要：

* 一个具有 Azure 存储帐户的 Azure 订阅。 如果没有 Azure 订阅，可以[注册免费试用版](https://azure.microsoft.com/free/)。
* 一个 Azure Active Directory 租户。
* 一个是该租户的全局管理员或安全管理员的用户。

## <a name="archive-logs-to-an-azure-storage-account"></a>将日志存档到 Azure 存储帐户

1. 登录到 [Azure 门户](https://portal.azure.com)。 
2. 单击“Azure Active Directory” -> “活动” -> “审核日志”。 
3. 单击“导出设置”以打开“诊断设置”边栏选项卡。 如果要更改现有设置，请单击“编辑设置”，若要要添加新设置，请单击“添加诊断设置”。 最多可以有三个设置。 
    ![导出设置](./media/reporting-azure-monitor-diagnostics-azure-storage-account/ExportSettings.png "导出设置")

4. 为设置添加友好名称以帮助你记住其用途。 例如，“发送到 Azure 存储帐户”。 
5. 选中“存档到存储帐户”复选框并单击“存储帐户”来选择 Azure 存储帐户。 
6. 选择要将日志路由到的 Azure 订阅和存储帐户，单击“确定”以关闭配置。
7. 选中 **AuditLogs** 复选框可将审核日志发送到存储帐户。 
8. 选中 **SignInLogs** 复选框可将登录日志发送到存储帐户。
9. 使用滑块来设置日志数据的保留期。 默认情况下，此值为“0”，日志将无限期地保留在存储帐户中。 另外，可以设置一个值，早于所选天数的事件将自动清除。
10. 单击“保存”以保存设置。
    ![诊断设置](./media/reporting-azure-monitor-diagnostics-azure-storage-account/DiagnosticSettings.png "诊断设置")

11. 在大约 15 分钟后，验证日志是否已推送到存储帐户。 转到 Azure 门户，单击“存储帐户”，选择之前使用的存储帐户并单击“Blob”。 
12. 对于“审核日志”，单击“insights-log-audit”。 对于“登录日志”，单击“insights-logs-signin”。
    ![存储帐户](./media/reporting-azure-monitor-diagnostics-azure-storage-account/StorageAccount.png "存储帐户")

## <a name="next-steps"></a>后续步骤

* [解释 Azure Monitor 中的审核日志架构](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [解释 Azure Monitor 中的登录日志架构](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [常见问题解答和已知的问题](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)