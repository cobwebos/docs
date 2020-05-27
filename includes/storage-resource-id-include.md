---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/15/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 39b61c78cbd0333183c80234256d6041a109d846
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2020
ms.locfileid: "68249918"
---
Azure AD 资源 ID 指示一个受众，其令牌在发出后可以用于提供对 Azure 资源的访问权限。 在使用 Azure 存储时，资源 ID 可能特定于单个存储帐户，也可能适用于任何存储帐户。 下表介绍可为资源 ID 提供的值：

|资源 ID  |说明  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | 给定存储帐户的服务终结点。 使用此值可获取一个用于对请求授权的令牌，这些请求仅针对该特定 Azure 存储帐户和服务。 将括号中的值替换为存储帐户的名称。      |
|`https://storage.azure.com/`     | 用于获取一个可对请求授权的令牌，这些请求针对任何 Azure 存储帐户。        |
