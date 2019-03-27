---
title: include 文件
description: include 文件
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: e8888a0505a3a38d2844f82c0f7fff255d05353d
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58261473"
---
在 Azure Cloud Shell 中，使用 [`az webapp deployment user set`](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) 命令配置部署凭据。 对 Web 应用进行 FTP 和本地 Git 部署时需要此部署用户。 用户名和密码处于帐户级别。 它们与 Azure 订阅凭据不同。

在下例中，将 \<username> 和 \<password>（包括括号）替换为新的用户名和密码。 用户名必须在 Azure 中唯一。 密码必须至少为 8 个字符，且具有字母、数字和符号这三种元素中的两种。 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

你会收到一个 JSON 输出，其中密码显示为 `null`。 如果收到 `'Conflict'. Details: 409` 错误，请更改用户名。 如果收到 ` 'Bad Request'. Details: 400` 错误，请使用更强的密码。

只需配置此部署用户一次。 然后可将其用于所有 Azure 部署。

> [!NOTE]
> 请记下用户名和密码。 之后需要它们来部署 Web 应用。
>
>
