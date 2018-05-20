---
title: Azure Active Directory 应用程序代理中的 GDPR | Microsoft Docs
description: 详细了解 Azure Active Directory 应用程序代理中的 GDPR。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: a3df15743b4918d72fac5f8769a2d3ee721a452f
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
---
# <a name="gdpr-in-the-azure-active-directory-application-proxy"></a>Azure Active Directory 应用程序代理中的 GDPR  

Azure Active Directory (Azure AD) 应用程序代理与 GDPR 以及其他所有 Microsoft 服务和功能兼容。 若要详细了解 Microsoft 对 GDPR 的支持，请参阅[许可条款和文档](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)。

由于此功能会在计算机上安装连接器，因此，在某些情况下，需要执行监视，以便持续符合 GDPR 的要求。 应用程序代理会创建可以包含 EUII 的以下日志类型：

- 连接器事件日志
- Windows 事件日志

以下是保证符合 GDPR 的两种方式：

- 删除和导出所发出的请求

- 关闭日志记录

对于：

- 有关如何为 Windows 事件日志配置数据保留的详细信息，请参阅[事件日志的设置](https://technet.microsoft.com/library/cc952132.aspx)。 
- 有关 Windows 事件日志的常规信息，请参阅[使用 Windows 事件日志](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx)。


也可以在 `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace` 中查找连接事件日志。 以下部分提供连接器事件日志的相关步骤。 必须在所有连接器计算机上完成这些步骤。
 

## <a name="processing-requests"></a>处理请求

你要负责处理三种不同类型的请求： 

- 删除
- 查看
- 导出
 
若要处理查看/导出请求，需要浏览每个日志文件，以搜索相关的条目。 

由于日志是文本文件，因此，可以使用类似于 `findstr` 的命令来搜索用户相关的条目。 搜索以下可能会出现在日志中的字段： 

- UserId
- 使用 Kerberos 约束委派为任何应用程序配置的用户名类型：
    - 用户主体名称
    - 本地用户主体名称
    - 用户主体名称的用户名部分
    - 本地用户主体名称的用户名部分
    - 本地 SAM 帐户名 

 
然后，可以收集这些字段并与用户共享。
若要处理删除请求，需要删除相关的日志。 可以重启连接器服务（Microsoft Azure AD 应用程序代理连接器）以生成新的日志文件。 生成新日志文件后，可以删除旧日志文件。 然后，可以遵循处理查看/导出请求的过程来查找所有相关日志，并选择性地删除这些字段或文件。 如果不再需要旧日志文件，始终可以将其全部删除。


## <a name="turn-off-connector-logs"></a>关闭连接器日志

若要关闭连接器日志，需要通过删除突出显示的行来调整 `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`： 


![配置](./media/active-directory-application-proxy-gdpr/01.png)


## <a name="next-steps"></a>后续步骤

有关 Azure AD 应用程序代理的概述，请参阅[如何提供对本地应用程序的安全远程访问](manage-apps/application-proxy.md)。

