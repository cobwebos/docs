---
title: 删除个人数据 - Azure Active Directory 应用程序代理 | Microsoft Docs
description: 从安装在设备上的连接器中删除 Azure Active Directory 应用程序代理的个人数据。
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: bccda196be82808e7dc369de3f517490f410e26e
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366036"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>删除 Azure Active Directory 应用程序代理的个人数据  

Azure Active Directory 应用程序代理要求你在设备上安装连接器，这意味着设备上可能存在个人数据。 本文提供了如何删除该个人数据以提高隐私的步骤。 


## <a name="where-is-the-personal-data"></a>个人数据在哪里？
应用程序代理可以将个人数据写入以下日志类型：

- 连接器事件日志
- Windows 事件日志

## <a name="remove-personal-data-from-windows-event-logs"></a>从 Windows 事件日志中删除个人数据

有关如何为 Windows 事件日志配置数据保留的信息，请参阅[事件日志的设置](https://technet.microsoft.com/library/cc952132.aspx)。 若要了解有关 Windows 事件日志的信息，请参阅[使用 Windows 事件日志](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>从连接器事件日志中删除个人数据

若要确保应用程序代理日志没有个人数据，可以：

- 在需要时删除或查看数据，或者
- 关闭日志记录

使用以下部分从连接器事件日志中删除个人数据。 对于安装了连接器的所有设备，必须完成删除过程。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>查看或导出特定数据

若要查看或导出特定数据，请在每个连接器事件日志中搜索相关条目。 日志位于 `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`。 

由于日志是文本文件，可以使用 [findstr](https://docs.microsoft.com/windows-server/administration/windows-commands/findstr) 来搜索与用户相关的文本项。  

若要查找个人数据，请在日志文件中搜索 UserID。 

若要查找由使用 Kerberos 约束委派的应用程序记录的个人数据，请搜索用户名类型的这些组件：

- 本地用户主体名称
- 用户主体名称的用户名部分
- 本地用户主体名称的用户名部分
- 本地安全帐户管理器 (SAM) 帐户名 


### <a name="delete-specific-data"></a>删除特定数据

若要删除特定数据，请执行以下操作：

1. 重启 Microsoft Azure AD 应用程序代理连接器服务以生成新的日志文件。 生成新日志文件后，可以删除或修改旧日志文件。 
2. 按照前面介绍的[查看或导出特定数据](#view-or-export-specific-data)过程，查找需要删除的信息。 搜索所有连接器日志。
3. 删除相关日志文件，或者有选择地删除包含个人数据的字段。 如果不再需要旧日志文件，也可以将其全部删除。

### <a name="turn-off-connector-logs"></a>关闭连接器日志

确保连接器日志不包含个人数据的一个选项是关闭日志生成。 若要停止生成连接器日志，请从 `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config` 中删除以下突出显示的行。 

![配置](./media/application-proxy-remove-personal-data/01.png)


## <a name="next-steps"></a>后续步骤

有关应用程序代理的概述，请参阅[如何提供对本地应用程序的安全远程访问](application-proxy.md)。

