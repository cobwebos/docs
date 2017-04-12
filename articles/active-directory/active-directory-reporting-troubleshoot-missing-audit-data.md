---
title: "故障排除：Azure Active Directory 活动日志中缺少数据 - 预览 | Microsoft 文档"
description: "列出 Azure Active Directory 预览的各种可用报表"
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
ms.date: 03/09/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: c372fe5f3a419a6a27ef00d755d5d46325b956c6
ms.lasthandoff: 04/06/2017


---

# <a name="i-cant-find-some-actions-that-i-performed-in-the-azure-active-directory-activity-log"></a>在 Azure Active Directory 活动日志中找不到已执行的某些操作


## <a name="symptoms"></a>症状

我在 Azure 门户中执行了一些操作，本应在`Activity logs > Audit Logs`边栏选项卡中看到这些操作的审核日志，但却找不到。

 ![报告](./media/active-directory-reporting-troubleshoot-missing-audit-data/01.png)
 

## <a name="cause"></a>原因

操作不会立即显示在“活动审核”日志中。 从执行操作开始算起，可能需要 15 分钟到 1 小时才能在门户中看到审核日志。

## <a name="resolution"></a>解决方法

等待 15 分钟到 1 小时，然后再看操作是否显示在日志中。 如果仍未看到操作，请向我们提交一个支持票证，我们会进行调查。


## <a name="next-steps"></a>后续步骤
请参阅 [Azure Active Directory 报告常见问题解答](active-directory-reporting-faq.md)。


