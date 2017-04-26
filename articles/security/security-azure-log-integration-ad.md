---
title: "Azure 日志集成 (AZLOG) 与集成 Active Directory 审核日志 | Microsoft Docs"
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
ms.date: 03/29/2017
ms.author: barclayn
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: f7c2f702a4ff2af8bb7487d49f5c6f9bf5199a49
ms.lasthandoff: 04/14/2017


---

#<a name="integrate-azure-active-directory-audit-logs"></a>集成 Azure Active Directory 审核日志

Azure Active Directory 审核事件可以帮助识别 Azure Active Directory 中发生的特权操作。 通过查看 [Azure Active Directory 审核报告事件](/active-directory/active-directory-reporting-audit-events#list-of-audit-report-events.md)，可以了解能够跟踪的事件类型

>[!NOTE]
必须已经成功完成[入门](security-azure-log-integration-get-started.md)文章中的步骤才能执行本文中的步骤。

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>集成 Azure Active Directory 审核日志的步骤

1. 打开命令提示符并键入 cd **c:\Program Files\Microsoft Azure Log Integration**
2. 运行以下命令：

 ``azlog createazureid``
 
 此命令提示你登录 Azure。 此命令接着会在 Azure AD Tenant 中创建一个 Azure Active Directory 服务主体，这些 Azure AD Tenant 会托管 Azure 订阅，而在这些 Azure 订阅中，已登录的用户是管理员、协同管理员或所有者。 如果已登录的用户仅仅是 Azure AD Tenant 中的一名来宾用户，那么此命令将失败。 对 Azure 的身份验证是通过 Azure Active Directory (AD) 完成的。 为 Azlog 集成创建服务主体会创建将被赋予从 Azure 订阅的读取权限的 Azure AD ID。
 
3. 运行提供 tenantID 的命令。 需要成为租户管理员角色的成员才能运行此命令。

``Azlog.exe authorizedirectoryreader tenantId``

示例

``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

检查以下文件夹，确认其中是否创建了 Azure Active Directory 审核日志 JSON 文件：

* **C:\Users\azlog\AzureActiveDirectoryJson**
* **C:\Users\azlog\AzureActiveDirectoryJsonLD**

将标准 SIEM 文件转发器连接器指向相应的文件夹，以将数据发送给 SIEM 实例。 根据所使用的 SIEM 产品，你可能需要某些字段映射。

通过 [Azure 日志集成 MSDN 论坛](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration)可以获得社区帮助。 在此论坛中，AzLog 社区可以对如何充分利用 Azure 日志集成相互提问和回答，并提供提示和技巧。 此外，Azure 日志集成团队也会关注此论坛，并尽可能地提供帮助。 

你也可以打开[支持请求](../azure-supportability/how-to-create-azure-support-request.md)。 若要执行此操作，请选择“日志集成”作为需要请求支持的服务。

## <a name="next-steps"></a>后续步骤
若要了解有关 Azure 日志集成的详细信息，请参阅以下文档：

* [适用于 Azure 日志的 Microsoft Azure 日志集成](https://www.microsoft.com/download/details.aspx?id=53324) - 下载中心，其中包含有关 Azure 日志集成的详细信息、系统要求和安装说明。
* [Azure 日志集成简介](security-azure-log-integration-overview.md)– 本文档介绍 Azure 日志集成、其主要功能及其工作原理。
* [合作伙伴配置步骤](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) - 此博客文章介绍如何配置 Azure 日志集成，以使用 Splunk、HP ArcSight 和 IBM QRadar 合作伙伴解决方案。
* [Azure 日志集成常见问题解答 (FAQ)](security-azure-log-integration-faq.md) - 此常见问题解答回答了有关 Azure 日志集成的问题。
* [集成安全中心警报与 Azure 日志集成](../security-center/security-center-integrating-alerts-with-log-integration.md) - 本文档介绍如何将安全中心警报以及由 Azure 诊断和 Azure 审核日志收集的虚拟机安全事件与你的日志分析或 SIEM 解决方案同步。
* [New features for Azure diagnostics and Azure Audit Logs](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/)（Azure 诊断和 Azure 审核日志的新功能）– 此博客文章介绍 Azure 审核日志和其他功能，可帮助你深入了解 Azure 资源的操作。

