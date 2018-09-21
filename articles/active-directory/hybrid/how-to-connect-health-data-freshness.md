---
title: Azure AD Connect Health -“运行状况服务数据不是最新的”警报 | Microsoft Docs
description: 本文档介绍了“运行状况服务数据不是最新的”警报的原因以及如何对此警报进行故障排除。
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: 430ea5f0a6f737d7632a4352c24d893368b80558
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2018
ms.locfileid: "46310376"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>“运行状况服务数据不是最新的”警报

## <a name="overview"></a>概述
<li>如果 Azure AD Connect Health 在两小时内未从服务器收到所有数据点，则它会生成数据刷新警报。 警报标题为“运行状况服务数据不是最新的”。 </li>
<li>如果 Connect Health 在两个小时内没有收到从服务器发送的部分数据元素，则会引发“警告”状态的警报。 “警告”状态的警报不会触发向租户管理员发送电子邮件通知。 </li>
<li>如果 Connect Health 在两个小时内没有收到从服务器发送的任何数据元素，则会引发“错误”状态的警报。 “错误”状态的警报会触发向租户管理员发送电子邮件通知。 </li>

>[!IMPORTANT] 
> 此警报遵守 Connect Health [数据保留策略](reference-connect-health-user-privacy.md#data-retention-policy)

## <a name="troubleshooting-steps"></a>疑难解答步骤 
* 请务必仔细审阅并满足[要求部分](how-to-connect-health-agent-install.md#requirements)。
* 使用[测试连接工具](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)来发现连接问题。


## <a name="next-steps"></a>后续步骤
* [Azure AD Connect Health 常见问题](reference-connect-health-faq.md)
