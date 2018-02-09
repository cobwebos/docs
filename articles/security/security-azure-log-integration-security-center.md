---
title: "Azure 日志集成与安全中心 | Microsoft Docs"
description: "了解如何将 Azure 安全中心警报用于日志集成"
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
ms.date: 08/29/2017
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 9acce21d544a43adcd0c0983771c02c6bb39caec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-get-your-security-center-alerts-in-azure-log-integration"></a>如何在 Azure 日志集成中获取安全中心警报

本文介绍了启用 Azure 日志集成服务的所需步骤，以此来获取 Azure 安全中心生成的安全警报信息。 必须已经成功完成[入门](security-azure-log-integration-get-started.md)文章中的步骤才能执行本文中的步骤。

## <a name="detailed-steps"></a>详细步骤

以下步骤将创建所需的 Azure Active Directory 服务主体，并为订阅分配服务主体读取权限：
1. 打开命令提示符并导航到 **c:\Program Files\Microsoft Azure Log Integration**
2. 运行命令 ``azlog createazureid``

    此命令提示登录 Azure。 此命令接着会在 Azure AD Tenant 中创建一个 [Azure Active Directory Service Principal](../active-directory/develop/active-directory-application-objects.md)，这些 Azure AD Tenant 会托管 Azure 订阅，而在这些 Azure 订阅中，已登录的用户是管理员、协同管理员或所有者。 如果已登录的用户仅仅是 Azure AD Tenant 中的一名来宾用户，那么此命令会失败。 对 Azure 的身份验证是通过 Azure Active Directory (AD) 完成的。 为 Azlog 集成创建服务主体会创建会被赋予从 Azure 订阅的读取权限的 Azure AD ID。

3. 接下来运行一条命令，将订阅的读取器访问权限分配给刚刚创建的服务主体。 如果未指定 SubscriptionID，那么命令会尝试将服务主体读取器角色分配给你有权访问的所有订阅。 </br></br>
``azlog authorize <SubscriptionID>`` </br> 例如 </br>
``azlog authorize 0ee55555-0bc4-4a32-a4e8-c29980000000``

    >[!NOTE]
    在运行 ```createazureid``` 命令之后立即运行授权命令，可能会出现警告。 Azure AD 帐户在创建之后，需要经过一段延迟时间才能使用。 如果在运行 ```createazureid``` 命令之后，等待大约 60 秒再运行授权命令，则不会出现警告。

4. 检查以下文件夹以确认其中是否存在审核日志 JSON 文件：
 * **c:\Users\azlog\AzureResourceManagerJson**
 * **c:\Users\azlog\AzureResourceManagerJsonLD** </br></br>
5. 检查以下文件夹以确认其中是否存在安全中心警报：</br></br>
 * **c:\Users\azlog\AzureSecurityCenterJson**
 * **c:\Users\azlog\AzureSecurityCenterJsonLD** </br></br>

如果在安装和配置过程中遇到问题，请打开[支持请求](/azure-supportability/how-to-create-azure-support-request.md)，选择“日志集成”作为需要请求支持的服务。

## <a name="next-steps"></a>后续步骤
若要了解有关 Azure 日志集成的详细信息，请参阅以下文档：

* [适用于 Azure 日志的 Microsoft Azure 日志集成](https://www.microsoft.com/download/details.aspx?id=53324) - 下载中心，其中包含有关 Azure 日志集成的详细信息、系统要求和安装说明。
* [Azure 日志集成简介](security-azure-log-integration-overview.md)– 本文档介绍 Azure 日志集成、其主要功能及其工作原理。
* [合作伙伴配置步骤](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) - 此博客文章介绍如何配置 Azure 日志集成，以使用 Splunk、HP ArcSight 和 IBM QRadar 合作伙伴解决方案。
* [Azure 日志集成常见问题解答 (FAQ)](security-azure-log-integration-faq.md) - 此常见问题解答回答了有关 Azure 日志集成的问题。
* [Azure 诊断和 Azure 审核日志的新功能](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – 此博客文章介绍 Azure 审核日志和其他功能，可帮助你深入了解 Azure 资源的操作。
