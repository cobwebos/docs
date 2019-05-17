---
title: 通知中心安全性
description: 本主题介绍 Azure 通知中心的安全性。
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 22494984ca45cde7255fb5e1a30548c859bfad68
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826510"
---
# <a name="security-model-of-azure-notification-hubs"></a>Azure 通知中心的安全模型

## <a name="overview"></a>概述

本主题介绍 Azure 通知中心的安全模型。 由于通知中心是服务总线实体，因此它们与服务总线实现同一安全模型。 有关详细信息，请参阅[服务总线身份验证](https://msdn.microsoft.com/library/azure/dn155925.aspx)主题。

## <a name="shared-access-signature-security-sas"></a>共享访问签名 (SAS) 安全性

通知中心实现称为 SAS（共享访问签名）的实体级安全方案。 此方案允许消息实体在其描述中最多声明 12 个用于向该实体授权的授权规则。

每个规则包含一个名称、一个密钥值（共享机密）和一组权限，如“安全声明”部分中所述。 在创建通知中心时，将自动创建两个规则：一个具有侦听权限（由客户端应用程序使用），一个具有所有权限（由应用程序后端使用）。

通过客户端应用执行注册管理时，如果通过通知发送的信息不敏感（例如，天气更新），则访问通知中心的常用方法是，向客户端应用提供规则仅限侦听访问权限的密钥值，并向应用后端提供规则完全访问权限的密钥值。

建议不要在 Windows 应用商店客户端应用中嵌入密钥值。 避免嵌入密钥值的一种方法是让客户端应用程序在启动时从应用程序后端检索密钥值。

必须知道，具有侦听访问权限的密钥允许客户端应用程序注册任何标记。 如果应用必须将特定标记的注册限制到特定的客户端（例如，当标记表示用户 ID 时），则应用后端必须执行注册。 有关详细信息，请参阅“注册管理”。 请注意，采用这种方式时，客户端应用将无权直接访问通知中心。

## <a name="security-claims"></a>安全声明

与其他实体类似，可以针对以下三种安全声明执行通知中心操作：侦听、发送和管理。

| 声明   | 描述                                          | 允许的操作 |
| ------- | ---------------------------------------------------- | ------------------ |
| 侦听  | 创建/更新、读取和删除单一注册。 | 创建/更新注册<br><br>读取注册<br><br>读取句柄的所有注册<br><br>删除注册 |
| 发送    | 向通知中心发送消息                | 发送消息 |
| 管理  | 通知中心的 CRUD（包括更新 PNS 凭据和安全密钥），以及基于标记读取注册。 |创建/更新/读取/删除通知中心<br><br>按标记读取注册 |

通知中心接受 Microsoft Azure 访问控制令牌所授予的声明，并接受签名令牌（使用在通知中心直接配置的共享密钥生成）所授予的声明。

不能将通知发送到多个命名空间。 命名空间是通知中心的逻辑容器，并不涉及发送通知。
命名空间级别访问策略 （凭据） 可用于命名空间级的操作，例如： 列出通知中心、 创建或删除通知中心，等等。只有中心级别访问策略将允许您发送通知。
