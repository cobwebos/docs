---
title: include 文件
description: include 文件
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 4be8821a949527fefcc9005b1de7f4f7c438c568
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "90943490"
---
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Python](https://www.python.org/downloads/) 2.7、3.5 或更高版本。
- 活动的通信服务资源和连接字符串。 [创建通信服务资源](../create-communication-resource.md)。

## <a name="setting-up"></a>设置

### <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

1. 打开终端或命令窗口，为应用创建一个新目录，并导航到该目录。

   ```console
   mkdir user-tokens-quickstart && cd user-tokens-quickstart
   ```

1. 使用文本编辑器在项目根目录中创建名为“issue-tokens.py”的文件，并添加程序的结构，包括基本异常处理。 将在以下部分中将此快速入门的所有源代码添加到此文件。

   ```python
   import os
   from azure.communication.administration import CommunicationIdentityClient

   try:
      print('Azure Communication Services - User Access Tokens Quickstart')
      # Quickstart code goes here
   except Exception as ex:
      print('Exception:')
      print(ex)
   ```

### <a name="install-the-package"></a>安装包

仍在应用程序目录中时，使用 `pip install` 命令安装适用于 Python 包的 Azure 通信服务管理客户端库。

```console
pip install azure-communication-administration
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>验证客户端

使用连接字符串实例化 `CommunicationIdentityClient`。 下面的代码从名为 `COMMUNICATION_SERVICES_CONNECTION_STRING` 的环境变量中检索资源的连接字符串。 了解如何[管理资源的连接字符串](../create-communication-resource.md#store-your-connection-string)。

在 `try` 块内添加此代码：

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ['COMMUNICATION_SERVICES_CONNECTION_STRING']

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

## <a name="create-a-user"></a>创建用户

Azure 通信服务维护轻量级标识目录。 使用 `create_user` 方法可在目录中创建具有唯一 `Id` 的新项。 应维护应用程序的用户与通信服务生成的标识之间的映射（例如，通过将它们存储在应用程序服务器的数据库中）。

```python
user = client.create_user()
print("\nCreated a user with ID: " + user.identifier + ":")
```

## <a name="issue-user-access-tokens"></a>颁发用户访问令牌

使用 `issue_token` 方法为通信服务用户颁发访问令牌。 如果未提供可选的 `user` 参数，将创建一个新用户并将其与令牌一起返回。

```python
# Issue an access token with the "voip" scope for a new user
token_result = client.issue_token(user, ["voip"])
expires_on = token_result.expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nIssued a token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

用户访问令牌是需要重新颁发的短期凭据，以防止用户遇到服务中断的情况。 `expires_on` 响应属性指示令牌的生存期。

## <a name="revoke-user-access-tokens"></a>调用用户访问令牌

在某些情况下，你可能需要显式撤销用户访问令牌，例如，当用户更改用于向服务进行身份验证的密码时。 此功能可通过 Azure 通信服务管理客户端库获取。

```python  
client.revoke_tokens(user)
print("\nSuccessfully revoked all tokens for user with ID: " + user.identifier)
```

## <a name="delete-a-user"></a>删除用户

删除标识将撤销所有活动令牌，并阻止你为标识颁发后续令牌。 它还会删除与用户关联的所有保存的内容。

```python
client.delete_user(user)
print("\nDeleted the user with ID: " + user.identifier)
```

## <a name="run-the-code"></a>运行代码

在控制台提示符下，导航到包含 issue-token.py 文件的目录，然后执行以下 `python` 命令来运行应用。

```console
python ./issue-token.py
```
