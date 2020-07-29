---
title: 通知中心安全模型
description: 了解 Azure 通知中心的安全模型。
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 09/23/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: b871775bc7a6d795e86147ae9cffa27bdd2f3348
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "76263755"
---
# <a name="notification-hubs-security"></a>通知中心安全性

## <a name="overview"></a>概述

本主题介绍 Azure 通知中心的安全模型。

## <a name="shared-access-signature-security"></a>共享访问签名安全性

通知中心实现称为共享访问签名 (SAS) 的实体级安全方案。  每个规则包含一个名称、一个密钥值（共享机密）和一组权限，如后面的[安全声明](#security-claims)中所述。 

在创建中心时，将自动创建两个规则：一个具有**侦听**权限（由客户端应用使用），一个具有**所有**权限（由应用后端使用）：

- **DefaultListenSharedAccessSignature**：仅授予**侦听**权限。
- **DefaultFullSharedAccessSignature**：授予**侦听**、**管理**和**发送**权限。 此策略将只用在应用后端中。 请勿将它用在客户端应用程序中；请使用只有“侦听”访问权限的策略。  若要使用新的 SAS 令牌创建新的自定义访问策略，请参阅本文后面的[用于访问策略的 SAS 令牌](#sas-tokens-for-access-policies)。

通过客户端应用执行注册管理时，如果通过通知发送的信息不敏感（例如，天气更新），则访问通知中心的常用方法是，向客户端应用提供规则仅限侦听访问权限的密钥值，并向应用后端提供规则完全访问权限的密钥值。

应用不应将密钥值嵌入 Windows Store 客户端应用中，而应让客户端应用在启动时从应用后端检索该值。

具有**侦听**访问权限的密钥允许客户端应用注册任何标记。 如果应用必须将特定标记的注册限制到特定的客户端（例如，当标记表示用户 ID 时），则应用后端必须执行注册。 有关详细信息，请参阅[注册管理](notification-hubs-push-notification-registration-management.md)。 请注意，采用这种方式时，客户端应用将无权直接访问通知中心。

## <a name="security-claims"></a>安全声明

与其他实体类似，可以针对以下三种安全声明执行通知中心操作：**侦听**、**发送**和**管理**。

| 声明   | 说明                                          | 允许的操作 |
| ------- | ---------------------------------------------------- | ------------------ |
| 侦听  | 创建/更新、读取和删除单一注册。 | 创建/更新注册<br><br>读取注册<br><br>读取句柄的所有注册<br><br>删除注册 |
| 发送    | 向通知中心发送消息                | 发送消息 |
| 管理  | 通知中心的 CRUD（包括更新 PNS 凭据和安全密钥），以及基于标记读取注册 |创建/更新/读取/删除中心<br><br>按标记读取注册 |

通知中心接受使用直接在中心配置的共享密钥生成的 SAS 令牌。

不能向多个命名空间发送通知。 命名空间是通知中心的逻辑容器，不参与发送通知。

将命名空间级别的访问策略（凭据）用于命名空间级别的操作，例如：列出中心、创建或删除中心，等等。只有中心级别访问策略才允许你发送通知。

### <a name="sas-tokens-for-access-policies"></a>用于访问策略的 SAS 令牌

若要创建新的安全声明或查看现有的 SAS 密钥，请执行以下操作：

1. 登录到 Azure 门户。
2. 选择“所有资源”， 
3. 选择要为其创建声明或要查看其 SAS 密钥的通知中心的名称。
4. 在左侧菜单中，选择“访问策略”  。
5. 选择“新建策略”，创建新的安全声明  。 为策略命名，并选择要授予的权限。 然后选择“确定”。 
6. 此时会在“访问策略”窗口中显示完整的连接字符串（包括新的 SAS 密钥）。 可以将该字符串复制到剪贴板，供以后使用。

若要从特定策略中提取 SAS 密钥，请选择包含所需 SAS 密钥的策略旁边的“复制”按钮。  将该值粘贴到某个临时位置中，然后复制连接字符串的 SAS 密钥部分。 此示例使用名为 **mytestnamespace1** 的通知中心命名空间，以及名为 **policy2** 的策略。 SAS 密钥是靠近字符串尾部的值，由 **SharedAccessKey** 指定：

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![获取 SAS 密钥](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>后续步骤

- [通知中心概述](notification-hubs-push-notification-overview.md)
