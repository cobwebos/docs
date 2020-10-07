---
title: include 文件
description: include 文件
services: azure-communication-services
author: chrwhit
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: chrwhit
ms.openlocfilehash: 76aae596c145c736ae75e65f7f72fdbdcead5919
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779504"
---
通过使用通信服务 Java 短信客户端库来发送短信，开启 Azure 通信服务使用旅程。

完成本快速入门会从你的 Azure 帐户中扣取最多几美分的费用。

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Artifact (Maven)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Java 开发工具包 (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true) 8 或更高版本。
- [Apache Maven](https://maven.apache.org/download.cgi)。
- 活动的通信服务资源和连接字符串。 [创建通信服务资源](../../create-communication-resource.md)。
- 启用短信的电话号码。 [获取电话号码](../get-phone-number.md)。

### <a name="prerequisite-check"></a>先决条件检查

- 在终端或命令窗口中，运行 `mvn -v` 以查看是否安装了 maven。
- 若要查看与通信服务资源关联的电话号码，请登录到 [Azure 门户](https://portal.azure.com/)，找到通信服务资源，然后从左侧导航窗格中打开“电话号码”选项卡。

## <a name="setting-up"></a>设置

### <a name="create-a-new-java-application"></a>创建新的 Java 应用程序

打开终端或命令窗口，并导航到要在其中创建 Java 应用程序的目录。 运行以下命令以从 maven-archetype-quickstart 模板生成 Java 项目。

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

“生成”目标将创建一个与 artifactId 名称相同的目录。 在此目录下，src/main/java 目录包含项目源代码，src/test/java 目录包含测试源，pom.xml 文件是项目的项目对象模型 (POM)  。

### <a name="install-the-package"></a>安装包

在文本编辑器中打开 pom.xml 文件****。 将以下依赖项元素添加到依赖项组。

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0-beta.2</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>设置应用框架

将 `azure-core-http-netty` 依赖项添加到 pom.xml 文件。

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core-http-netty</artifactId>
    <version>1.3.0</version>
</dependency>
```

在文本编辑器中打开 /src/main/java/com/communication/quickstart/App.java，添加 import 指令并删除 `System.out.println("Hello world!");` 语句：

```java
package com.communication.quickstart;

import java.io.IOException;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.util.ArrayList;
import java.util.List;

import com.azure.communication.common.CommunicationClientCredential;
import com.azure.communication.common.PhoneNumber;
import com.azure.communication.sms.SmsClient;
import com.azure.communication.sms.SmsClientBuilder;
import com.azure.communication.sms.models.SendSmsOptions;
import com.azure.communication.sms.models.SendSmsResponse;
import com.azure.core.http.HttpClient;
import com.azure.core.http.netty.NettyAsyncHttpClientBuilder;

public class App
{
    public static void main( String[] args ) throws IOException, NoSuchAlgorithmException, InvalidKeyException
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>对象模型

以下类和接口处理适用于 Java 的 Azure 通信服务短信客户端库的某些主要功能。

| 名称                                                             | 说明                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | 此类创建 SmsClient。 向其提供终结点、凭据和 http 客户端。 |
| SmsClient                    | 所有短信功能都需要此类。 用其发送短信。                |
| SendSmsResponse               | 此类包含来自短信服务的响应。                                          |
| CommunicationClientCredential | 此类处理签名请求。                                                            |
| PhoneNumber                   | 此类包含电话号码信息

## <a name="authenticate-the-client"></a>验证客户端

使用连接字符串实例化 `SmsClient`。 下面的代码从名为 `COMMUNICATION_SERVICES_ENDPOINT_STRING` 和 `COMMUNICATION_SERVICES_CREDENTIAL_STRING` 的环境变量中（凭据是 Azure 门户中的 `Key`）检索资源的终结点和凭据字符串。 了解如何[管理资源的连接字符串](../../create-communication-resource.md#store-your-connection-string)。

将以下代码添加到 `main` 方法中：

```java
// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationClientCredential credential = new CommunicationClientCredential(accessKey);

// Configure and build a new SmsClient
SmsClient client = new SmsClientBuilder()
    .endpoint(endpoint)
    .credential(credential)
    .httpClient(httpClient)
    .buildClient();
```

可以用任何实现 `com.azure.core.http.HttpClient` 接口的自定义 HTTP 客户端来初始化客户端。 上面的代码演示了如何使用 `azure-core` 提供的 [Azure Core Netty HTTP 客户端](https://docs.microsoft.com/java/api/overview/azure/core-http-netty-readme?view=azure-java-stable&preserve-view=true)。

## <a name="send-an-sms-message"></a>发送短信

通过调用 sendMessage 方法发送短信。 将此代码添加到 `main` 方法的末尾：

```java
List<PhoneNumber> to = new ArrayList<PhoneNumber>();
to.add(new PhoneNumber("<to-phone-number>"));

// SendSmsOptions is an optional field. It can be used
// to enable a delivery report to the Azure Event Grid
SendSmsOptions options = new SendSmsOptions();
options.setEnableDeliveryReport(true);

// Send the message and check the response for a message id
SendSmsResponse response = client.sendMessage(
    new PhoneNumber("<leased-phone-number>"),
    to,
    "<message-text>",
    options);

System.out.println("MessageId: " + response.getMessageId());
```

应将 `<leased-phone-number>` 替换为与通信服务资源关联的启用短信的电话号码，将 `<to-phone-number>` 替换为要向其发送消息的电话号码。

`enableDeliveryReport` 参数是一个可选参数，可用于配置传送报告。 这对于要在传送短信后发出事件的情况很有用。 请参阅[处理短信事件](../handle-sms-events.md)快速入门，了解如何为短信配置传送报告。

<!--todo: the signature of the `sendMessage` method changes when configuring delivery reporting. Need to confirm that this is how our client library is to be used.-->

## <a name="run-the-code"></a>运行代码

导航到包含 pom.xml 文件的目录，并使用 `mvn` 命令编译该项目。

```console

mvn compile

```

然后，生成包。

```console

mvn package

```

运行以下 `mvn` 命令以执行应用。

```console

mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false

```
