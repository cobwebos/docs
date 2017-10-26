---
title: "故障排除：Azure Active Directory 活动日志中缺少数据 | Microsoft Docs"
description: "列出 Azure Active Directory 的各种可用报告"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 4e63c9b48125e3c7283be5fe88cab211509f88f4
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2017
---
# <a name="i-cant-find-some-actions-that-i-performed-in-the-azure-active-directory-activity-log"></a>在 Azure Active Directory 活动日志中找不到已执行的某些操作


## <a name="symptoms"></a>症状

我在 Azure 门户中执行了一些操作，本应在`Activity logs > Audit Logs`边栏选项卡中看到这些操作的审核日志，但却找不到。

 ![报告](./media/active-directory-reporting-troubleshoot-missing-audit-data/01.png)
 

## <a name="cause"></a>原因

操作不会立即显示在“活动审核”日志中。 从执行操作开始算起，可能需要 15 分钟到 1 小时才能在门户中看到审核日志。

## <a name="resolution"></a>解决方法

等待 15 分钟到 1 小时，再看操作是否显示在日志中。 如果仍未看到操作，请向我们提交一个支持票证，我们会进行调查。


## <a name="next-steps"></a>后续步骤
请参阅 [Azure Active Directory 报告常见问题解答](active-directory-reporting-faq.md)。

