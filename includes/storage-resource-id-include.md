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
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249918"
---
Azure AD 的资源 ID 指示可以使用颁发的令牌来提供对 Azure 资源的访问权限的受众。 对于 Azure 存储, 资源 ID 可能特定于单个存储帐户, 也可能适用于任何存储帐户。 下表描述了可为资源 ID 提供的值:

|资源 ID  |描述  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | 给定存储帐户的服务终结点。 使用此值获取令牌, 以将请求仅授权给特定的 Azure 存储帐户和服务。 将括号中的值替换为你的存储帐户的名称。      |
|`https://storage.azure.com/`     | 用于获取用于向任何 Azure 存储帐户授权请求的令牌。        |
