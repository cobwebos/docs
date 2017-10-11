---
title: "Azure 日志集成与 Azure Active Directory 审核日志 | Microsoft Docs"
description: "了解如何安装 Azure 日志集成服务和集成来自 Azure 审核日志的日志"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/08/2017
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 8a1295cc86057ed72940e774d0bd423d61142e31
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="integrate-azure-active-directory-audit-logs"></a>集成 Azure Active Directory 审核日志

Azure Active Directory (Azure AD) 审核事件可以帮助识别 Azure Active Directory 中发生的特权操作。 通过查看 [Azure Active Directory 审核报告事件](/active-directory/active-directory-reporting-audit-events#list-of-audit-report-events.md)，可以了解能够跟踪的事件类型。

> [!NOTE]
> 必须先查看[入门](security-azure-log-integration-get-started.md)文章并完成其中的步骤，然后才能尝试本文中的步骤。

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>集成 Azure Active Directory 审核日志的步骤

1. 打开命令提示符并运行此命令：

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. 运行以下命令： 
 
   ``azlog createazureid``

   此命令提示登录 Azure。 此命令接着会在 Azure AD Tenant 中创建一个 Azure Active Directory 服务主体，这些 Azure AD Tenant 会托管 Azure 订阅，而在这些 Azure 订阅中，已登录的用户是管理员、协同管理员或所有者。 如果已登录的用户仅仅是 Azure AD Tenant 中的一名来宾用户，那么此命令会失败。 通过 Azure AD 完成 Azure 身份验证。 为 Azure 日志集成创建服务主体会创建 Azure AD 标识，后者已获得相关访问权限，可以从 Azure 订阅进行读取。

3. 运行以下命令以提供租户 ID。 需要成为租户管理员角色的成员才能运行此命令。

   ``Azlog.exe authorizedirectoryreader tenantId``

   示例：

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. 检查以下文件夹，确认其中是否创建了 Azure Active Directory 审核日志 JSON 文件：

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

以下视频演示本文中所述的步骤：

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> 有关将 JSON 文件中的信息添加到安全信息和事件管理 (SIEM) 系统的具体说明，请联系 SIEM 供应商。

通过 [Azure 日志集成 MSDN 论坛](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration)可以获得社区帮助。 在该论坛中，Azure 日志集成社区的人员可以通过问题、答案、提示和技巧相互支持。 此外，Azure 日志集成团队也会关注此论坛，并尽可能地提供帮助。

也可以打开[支持请求](../azure-supportability/how-to-create-azure-support-request.md)。 请选择“日志集成”作为需要请求支持的服务。

## <a name="next-steps"></a>后续步骤
若要了解有关 Azure 日志集成的详细信息，请参阅：

* [适用于 Azure 日志的 Microsoft Azure 日志集成](https://www.microsoft.com/download/details.aspx?id=53324)：此“下载中心”页面提供了有关 Azure 日志集成的详细信息、系统要求和安装说明。
* [Azure 日志集成简介](security-azure-log-integration-overview.md)：本文介绍 Azure 日志集成、其主要功能及其工作原理。
* [合作伙伴配置步骤](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/)：此博客文章介绍如何配置 Azure 日志集成，以使用 Splunk、HP ArcSight 和 IBM QRadar 合作伙伴解决方案。
* [Azure 日志集成常见问题解答](security-azure-log-integration-faq.md)：本文回答有关 Azure 日志集成的问题。
* [集成安全中心警报与 Azure 日志集成](../security-center/security-center-integrating-alerts-with-log-integration.md)：本文介绍如何将安全中心警报以及由 Azure 诊断和 Azure 审核日志收集的虚拟机安全事件与日志分析或 SIEM 解决方案同步。
* [Azure 诊断和 Azure 审核日志的新功能](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/)：此博客文章介绍 Azure 审核日志和其他功能，可帮助你深入了解 Azure 资源的操作。
