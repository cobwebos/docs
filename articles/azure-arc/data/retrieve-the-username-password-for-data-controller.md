---
title: 检索用于连接到 Arc 数据控制器的用户名和密码
description: 检索用于连接到 Arc 数据控制器的用户名和密码
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 8cabb48e3620f1a17d1bb9b87e1646ce2793143b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761696"
---
# <a name="retrieve-the-user-name-and-password-to-connect-to-the-arc-data-controller"></a>检索用于连接到 Arc 数据控制器的用户名和密码

您可能会遇到需要检索数据控制器的用户名和密码的情况。 这是运行时所需的命令。 

```console
azdata login
```

如果你是群集的 Kubernetes 管理员。 因此，你具有运行命令以从 Kubernetes 密钥中检索的权限存储 Azure Arc 在该处保存的信息。

> [!NOTE]
>  如果在创建数据控制器的命名空间中使用了不同的名称，请确保 `-n arc` 在下面的命令中更改参数，以使用已创建数据控制器的命名空间的名称。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="linux"></a>Linux

运行以下命令以检索用户名：

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}" | base64 -d
```

运行以下命令以检索密码：

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}" | base64 -d
```

## <a name="powershell"></a>PowerShell

运行以下命令以检索用户名：

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}")))
```

运行以下命令以检索密码：

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}")))
```

## <a name="next-steps"></a>后续步骤

试用其他 [方案](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory-domain-services/scenarios.md)
