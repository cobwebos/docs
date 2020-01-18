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
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263755"
---
# <a name="notification-hubs-security"></a>通知中心安全性

## <a name="overview"></a>概述

本主题介绍 Azure 通知中心的安全模型。

## <a name="shared-access-signature-security"></a>共享访问签名安全性

通知中心实现称为*共享访问签名*（SAS）的实体级安全方案。 每个规则包含一个名称、一个密钥值（共享机密）和一组权限，如稍后的[安全声明](#security-claims)中所述。 

创建中心时，会自动创建两个规则：一个具有**侦听**权限（客户端应用程序使用），一个具有**所有**权限（应用后端使用）：

- **DefaultListenSharedAccessSignature**：仅授予**侦听**权限。
- **DefaultFullSharedAccessSignature**：授予**侦听**、**管理**和**发送**权限。 此策略仅在应用后端中使用。 请勿在客户端应用程序中使用;使用仅具有**侦听**访问权限的策略。 若要使用新的 SAS 令牌创建新的自定义访问策略，请参阅本文后面的[访问策略的 SAS 令牌](#sas-tokens-for-access-policies)。

通过客户端应用执行注册管理时，如果通过通知发送的信息不敏感（例如，天气更新），则访问通知中心的常用方法是，向客户端应用提供规则仅限侦听访问权限的密钥值，并向应用后端提供规则完全访问权限的密钥值。

应用不应在 Windows 应用商店客户端应用中嵌入密钥值;而是让客户端应用程序在启动时从应用程序后端检索它。

具有**侦听**访问权限的密钥允许客户端应用程序注册任何标记。 如果应用必须将特定标记的注册限制到特定的客户端（例如，当标记表示用户 Id 时），则应用后端必须执行注册。 有关详细信息，请参阅[注册管理](notification-hubs-push-notification-registration-management.md)。 请注意，采用这种方式时，客户端应用将无权直接访问通知中心。

## <a name="security-claims"></a>安全声明

与其他实体类似，允许通知中心操作三个安全声明：**侦听**、**发送**和**管理**。

| 声明   | Description                                          | 允许的操作 |
| ------- | ---------------------------------------------------- | ------------------ |
| 侦听  | 创建/更新、读取和删除单一注册。 | 创建/更新注册<br><br>读取注册<br><br>读取句柄的所有注册<br><br>删除注册 |
| 发送    | 向通知中心发送消息                | 发送消息 |
| 管理  | 通知中心的 CRUD（包括更新 PNS 凭据和安全密钥），以及基于标记读取注册。 |创建/更新/读取/删除中心<br><br>按标记读取注册 |

通知中心接受通过直接在中心配置的共享密钥生成的 SAS 令牌。

不能将通知发送到多个命名空间。 命名空间是通知中心的逻辑容器，不涉及发送通知。

将命名空间级别的访问策略（凭据）用于命名空间级操作;例如：列出中心、创建或删除中心等。只有中心级别的访问策略可以发送通知。

### <a name="sas-tokens-for-access-policies"></a>访问策略的 SAS 令牌

若要创建新的安全声明或查看现有 SAS 密钥，请执行以下操作：

1. 登录到 Azure 门户。
2. 选择“所有资源”，
3. 选择要为其创建声明或查看 SAS 密钥的通知中心的名称。
4. 在左侧菜单中，选择 "**访问策略**"。
5. 选择 "**新建策略**" 以创建新的安全声明。 为策略指定一个名称，并选择要授予的权限。 然后选择“确定”。
6. 完整连接字符串（包括新的 SAS 密钥）会显示在 "访问策略" 窗口中。 可以将此字符串复制到剪贴板供以后使用。

若要从特定策略中提取 SAS 密钥，请选择包含所需 SAS 密钥的策略旁边的 "**复制**" 按钮。 将此值粘贴到临时位置，然后复制连接字符串的 SAS 密钥部分。 此示例使用一个名为**mytestnamespace1**的通知中心命名空间和一个名为**policy2**的策略。 SAS 密钥是**SharedAccessKey**指定的字符串末尾附近的值：

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![获取 SAS 密钥](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>后续步骤

- [通知中心概述](notification-hubs-push-notification-overview.md)
