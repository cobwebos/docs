---
title: 如何使用 Azure Monitor（预览版）将 Azure Active Directory 日志与 ArcSight 集成 | Microsoft Docs
description: 了解如何使用 Azure Monitor（预览版）将 Azure Active Directory 日志与 ArcSight 集成
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: b37bef0d-982e-4e28-86b2-6c61ca524ae1
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 12/03/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88979d58c78f3bc44c7fa96c65fb60c0fb69726b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175250"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor-preview"></a>使用 Azure Monitor（预览版）将 Azure Active Directory 日志与 ArcSight 集成

[Micro Focus ArcSight](https://software.microfocus.com/products/siem-security-information-event-management/overview) 是一种安全信息和事件管理 (SIEM) 解决方案，可帮助检测和响应平台中的安全威胁。 现在可以使用适用于 Azure AD 的 ArcSight 连接器，通过 Azure Monitor 将 Azure Active Directory (Azure AD) 日志路由到 ArcSight。 此功能允许使用 ArcSight 监视租户中是否存在安全泄漏。  

在本文中，你会了解如何使用 Azure Monitor 将 Azure AD 日志路由到 ArcSight。 

## <a name="prerequisites"></a>先决条件

若要使用此功能，需满足以下条件:
* 包含 Azure AD 活动日志的 Azure 事件中心。 了解如何[将活动日志流式传输到事件中心](quickstart-azure-monitor-stream-logs-to-event-hub.md)。 
* ArcSight Syslog NG 守护程序 SmartConnector (SmartConnector) 或 ArcSight 负载均衡器的已配置实例。 如果事件发送到 ArcSight 负载均衡器，则它们会因此由负载均衡器发送到 SmartConnector。

下载并打开[适用于 Azure Monitor 事件中心的 ArcSight SmartConnector 配置指南](https://community.softwaregrp.com/dcvta86296/attachments/dcvta86296/connector-documentation/1232/2/Microsoft%20Azure%20Monitor%20Event%20Hub.pdf)。 本指南包含安装和配置适用于 Azure Monitor 的 ArcSight SmartConnector 所需的步骤。 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>将 Azure AD 日志与 ArcSight 集成

1. 首先，完成配置指南“先决条件”部分中的步骤。 此部分包括下列步骤：
    * 在 Azure 中设置用户权限，以确保某个用户具有“所有者”角色，可部署和配置连接器。
    * 在具有 Syslog NG 守护程序 SmartConnector 的服务器上打开端口，以便可从 Azure 访问它。 
    * 部署会运行 Windows PowerShell 脚本，因此必须启用 PowerShell 以在要部署连接器的计算机上运行脚本。

2. 按照配置指南“部署连接器”部分中的步骤来部署连接器。 此部分会演示如何下载和提取连接器、配置应用程序属性以及从提取的文件夹运行部署脚本。 

3. 按照“在 Azure 中验证部署”部分中的步骤来确保连接器已设置并正常工作。 验证以下各项：
    * 在 Azure 订阅中创建了必要的 Azure 函数。
    * Azure AD 日志流式传输到正确的目标。 
    * 部署中的应用程序设置保留在 Azure Function App 的“应用程序设置”中。 
    * 使用适用于 ArcSight 连接器的 Azure AD 应用程序以及包含映射文件（CEF 格式）的存储帐户，在 Azure 中创建 ArcSight 的新资源组。

4. 最后，完成配置指南**部署后配置**中的部署后步骤。 此部分说明如何在实施应用服务计划时执行其他配置以防止 Function App 在超时期限之后成为空闲状态、从事件中心配置诊断日志流式传输以及更新 SysLog NG 守护程序 SmartConnector 密钥存储证书以将它与新创建的存储帐户相关联。

5. 配置指南还说明如何在 Azure 中自定义连接器属性以及如何升级和卸载连接器。 还有一个部分是关于性能改进，包括升级到 [Azure 消耗计划](https://azure.microsoft.com/pricing/details/functions)以及在事件负载大于单个 Syslog NG 守护程序 SmartConnector 可以处理的负载时配置 ArcSight 负载均衡器。

## <a name="next-steps"></a>后续步骤

* [适用于 Azure Monitor 事件中心的 ArcSight SmartConnector 配置指南](https://community.softwaregrp.com/dcvta86296/attachments/dcvta86296/connector-documentation/1232/2/Microsoft%20Azure%20Monitor%20Event%20Hub.pdf)
