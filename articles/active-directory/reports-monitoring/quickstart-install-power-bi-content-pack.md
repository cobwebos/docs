---
title: 安装 Azure AD Power BI 内容包 | Microsoft Docs
description: 了解如何安装 Azure AD Power BI 内容包
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: fd5604eb-1334-4bd8-bfb5-41280883e2b5
ms.service: active-directory
ms.workload: identity
ms.component: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/23/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: ba2784663a585aebb82ee149be3b54e73920f6dd
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816644"
---
# <a name="quickstart-install-azure-active-directory-power-bi-content-pack"></a>快速入门：安装 Azure Active Directory Power BI 内容包

|  |
|--|
|目前，Azure AD Power BI 内容包使用 Azure AD Graph API 检索 Azure AD 租户中的数据。 因此，你可能会发现内容包中可用数据与使用[用于报告的 Microsoft Graph API](concept-reporting-api.md) 检索的数据之间存在一些差异。 |
|  |

使用 Azure Active Directory 的 Power BI 内容包，可以深入了解你的 Active Directory 中正在发生的事情。 可以下载预建内容包并使用它通过 Power BI 提供的丰富可视化效果体验来报告你的目录中的所有活动。 还可以轻松创建自己的仪表板并将其与组织中的任何其他人共享。 

在本快速入门中，你将了解如何安装内容包。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，你需要：

* 一个 Power BI 帐户。 这是与你的 O365 或 Azure AD 帐户相同的帐户。 
* 你的 Azure AD 租户 ID。 这是从 Azure 门户的[属性页面](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)中获得的你的目录的**目录 ID**。
* 一个 Azure AD Premium (P1/P2) 许可证。 

## <a name="install-azure-ad-power-bi-content-pack"></a>安装 Azure AD Power BI 内容包 

1. 使用你的 Power BI 帐户登录到 [Power BI](https://app.powerbi.com/groups/me/getdata/services)。 这是与你的 O365 或 Azure AD 帐户相同的帐户。

2. 在“应用”页面中搜索 **Azure Active Directory 活动日志**并选择“立即获取”。 

   ![Azure Active Directory Power BI 内容包](./media/quickstart-install-power-bi-content-pack/getitnow.png) 
    
3. 在弹出窗口中，键入你的 Azure AD 租户 ID，输入 **7** 作为天数，然后选择“下一步”。
    
   ![Azure Active Directory Power BI 内容包](./media/quickstart-install-power-bi-content-pack/connect.png) 

4. 创建 Azure Active Directory 活动日志仪表板后，选择该仪表板。

   ![Azure Active Directory Power BI 内容包](./media/quickstart-install-power-bi-content-pack/dashboard.png) 
    
## <a name="next-steps"></a>后续步骤

* [使用 Power BI 内容包](howto-power-bi-content-pack.md)。
* [对内容包错误进行故障排除](troubleshoot-content-pack.md)。
