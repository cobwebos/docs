---
title: include 文件
description: include 文件
services: azure-communication-services
author: danieldoolabh
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 09/03/2020
ms.topic: include
ms.custom: include file
ms.author: dadoolab
ms.openlocfilehash: 3fe27bf31385e0310211b5e1b0b7bbfa636eb19b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943961"
---
通过使用通信服务 Python 短信客户端库来发送短信，开启 Azure 通信服务使用旅程。

完成本快速入门会从你的 Azure 帐户中扣取最多几美分的费用。

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](#todo-sdk-repo) | [Package (PiPy)](#todo-nuget) | [Samples](#todo-samples)--> 

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- [Python](https://www.python.org/downloads/) 2.7、3.5 或更高版本。
- 活动的通信服务资源和连接字符串。 [创建通信服务资源](../../create-communication-resource.md)。
- 启用短信的电话号码。 [获取电话号码](../get-phone-number.md)。

### <a name="prerequisite-check"></a>先决条件检查

- 在终端或命令窗口中，运行 `python --version` 命令来查看是否安装了 Python。
- 若要查看与通信服务资源关联的电话号码，请登录到 [Azure 门户](https://portal.azure.com/)，找到通信服务资源，然后从左侧导航窗格中打开“电话号码”选项卡。

## <a name="setting-up"></a>设置

### <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

打开终端或命令窗口，为应用创建一个新目录，并导航到该目录。

```console
mkdir sms-quickstart && cd sms-quickstart
```

使用文本编辑器在项目根目录中创建名为“send-sms.py”的文件，并添加程序的结构，包括基本异常处理。 将在以下部分中将此快速入门的所有源代码添加到此文件。

```python
import os
from azure.communication.sms import PhoneNumber
from azure.communication.sms import SendSmsOptions
from azure.communication.sms import SmsClient

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-the-package"></a>安装包

仍在应用程序目录中时，使用 `pip install` 命令安装适用于 Python 包的 Azure 通信服务短信客户端库。

```console
pip install azure-communication-sms
```

## <a name="object-model"></a>对象模型

以下类和接口处理适用于 Python 的 Azure 通信服务短信客户端库的某些主要功能。

| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | 所有短信功能都需要此类。 使用订阅信息对其进行实例化，然后使用它发送短信。 |
| SendSmsOptions | 此类提供用于配置传送报告的选项。 如果 enable_delivery_report 设置为 True，则在传送成功时将发出一个事件 |

## <a name="authenticate-the-client"></a>验证客户端

使用连接字符串实例化 SmsClient。 下面的代码从名为 `COMMUNICATION_SERVICES_CONNECTION_STRING` 的环境变量中检索资源的连接字符串。 了解如何[管理资源的连接字符串](../../create-communication-resource.md#store-your-connection-string)。

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.getenv('COMMUNICATION_SERVICES_CONNECTION_STRING')

# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(connection_string)
```

## <a name="send-an-sms-message"></a>发送短信

通过调用 Send 方法发送短信。 将此代码添加到 send-sms.py 中的 `try` 块的末尾：

```python

# calling send() with sms values
sms_response = sms_client.send(
        from_phone_number=PhoneNumber("<leased-phone-number>"),
        to_phone_numbers=[PhoneNumber("<to-phone-number>")],
        message="Hello World via SMS",
        send_sms_options=SendSmsOptions(enable_delivery_report=True)) # optional property

```

应将 `<leased-phone-number>` 替换为与通信服务关联的启用短信的电话号码，将 `<to-phone-number>` 替换为要向其发送消息的电话号码。 所有电话号码参数都应遵循 [E.164 标准](../../../concepts/telephony-sms/plan-solution.md#optional-reading-international-public-telecommunication-numbering-plan-e164)。

`send_sms_options` 参数是一个可选参数，可用于配置传送报告。 这对于要在传送短信后发出事件的情况很有用。 请参阅[处理短信事件](../handle-sms-events.md)快速入门，了解如何为短信配置传送报告。

## <a name="run-the-code"></a>运行代码

从应用程序目录使用 `python` 命令运行应用程序。

```console
python send-sms.py
```
