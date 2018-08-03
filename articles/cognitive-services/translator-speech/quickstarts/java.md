---
title: 适用于 Azure 认知服务中的 Microsoft 语音翻译 API 的 Java 快速入门 | Microsoft Docs
description: 获取信息和代码示例，有助于快速开始使用 Azure 上 Microsoft 认知服务中的 Microsoft 语音翻译 API。
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jaswel
ms.openlocfilehash: b06ee0c37d52f81db1a3bad6907690619002ef7c
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205497"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-java"></a>将 Microsoft 语音翻译 API 与 Java 配合使用的快速入门 
<a name="HOLTop"></a>

本文演示如何使用 Microsoft 语音翻译 API 翻译 .wav 文件中的口述内容。

## <a name="prerequisites"></a>先决条件

需要使用 [JDK 7 或 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 来编译和运行此代码。 可使用喜欢的 Java IDE（如果有）操作，但文本编辑器足以满足要求。

需要以下文件。
- [javax.websocket-api-1.1.jar（或更高版本）](https://mvnrepository.com/artifact/javax.websocket/javax.websocket-api)
- [jetty-http-9.4.11.v20180605.jar（或更高版本）](https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-http)
- [jetty-io-9.4.11.v20180605.jar（或更高版本）](https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-io)
- [jetty-util-9.4.11.v20180605.jar（或更高版本）](https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-util)
- [websocket-api-9.4.11.v20180605.jar（或更高版本）](https://mvnrepository.com/artifact/org.eclipse.jetty.websocket/websocket-api)
- [websocket-client-9.4.11.v20180605.jar（或更高版本）](https://mvnrepository.com/artifact/org.eclipse.jetty.websocket/websocket-client)
- [websocket-common-9.4.11.v20180605.jar（或更高版本）](https://mvnrepository.com/artifact/org.eclipse.jetty.websocket/websocket-common)
- [javax-websocket-client-impl-9.4.11.v20180605.jar（或更高版本）](https://mvnrepository.com/artifact/org.eclipse.jetty.websocket/javax-websocket-client-impl)
- [jetty-client-9.4.11.v20180605.jar（或更高版本）](https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-client)

将需要一个名为“speak.wav”的 .wav 文件，该文件与从以下代码编译的可执行文件位于同一文件夹中。 此 .wav 文件应采用标准 PCM、16 位、16 kHz 单声道格式。 可使用[文本转语音 API](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech) 获取此类 .wav 文件。

必须创建一个具有 Microsoft 语音翻译 API 的[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 需要一个来自 [Azure 仪表板](https://portal.azure.com/#create/Microsoft.CognitiveServices)的付费订阅密钥。

## <a name="translate-speech"></a>翻译语音

下面的代码将语音从一种语言翻译为另一种语言。

1. 在喜欢使用的 IDE 中新建一个 Java 项目。
2. 添加下方提供的代码。
3. 使用对订阅有效的访问密钥替换 `key` 值。
4. 运行该程序。

Config.java：

```java
import java.util.*;

import javax.websocket.ClientEndpointConfig;

public class Config extends ClientEndpointConfig.Configurator {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace the subscriptionKey string value with your valid subscription key.
    static String key = "ENTER KEY HERE";

    @Override
    public void beforeRequest (Map<String,List<String>> headers) {
        headers.put("Ocp-Apim-Subscription-Key", Arrays.asList (key));
    }
}
```

Client.java：

```java
import java.io.*;
import java.net.*;
import java.util.*;

import javax.websocket.ClientEndpoint;
import javax.websocket.CloseReason;
import javax.websocket.ContainerProvider;
import javax.websocket.OnClose;
import javax.websocket.OnError;
import javax.websocket.OnMessage;
import javax.websocket.OnOpen;
import javax.websocket.Session;
import javax.websocket.WebSocketContainer;

import org.eclipse.jetty.client.*;
import org.eclipse.jetty.http.*;
import org.eclipse.jetty.io.*;
import org.eclipse.jetty.util.*;
import org.eclipse.jetty.websocket.api.*;
import org.eclipse.jetty.websocket.client.io.*;
import org.eclipse.jetty.websocket.common.scopes.*;

/* Useful reference links:
    https://docs.oracle.com/javaee/7/api/javax/websocket/Session.html
    https://docs.oracle.com/javaee/7/api/javax/websocket/RemoteEndpoint.Basic.html
    https://docs.oracle.com/javaee/7/api/javax/websocket/OnMessage.html
    http://www.oracle.com/technetwork/articles/java/jsr356-1937161.html
*/

@ClientEndpoint(configurator = Config.class)
public class Client {
    static String host = "wss://dev.microsofttranslator.com";
    static String path = "/speech/translate";
    static String params = "?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa";
    static String uri = host + path + params;

    static String input_path = "speak.wav";
    static String output_path = "speak2.wav";

    Session session = null;

    @OnOpen
    public void onOpen(Session session) {
        this.session = session;
        System.out.println ("Connected.");
        SendAudio ();
    }

    @OnMessage
    public void onTextMessage(String message, Session session){
        System.out.println ("Text message received.");
        System.out.println (message);
    }

/*
Use the following signature to receive the message in parts:
    public void onBinaryMessage(byte[] buffer, boolean last, Session session)
Use the following signature to receive the entire message:
    public void onBinaryMessage(byte[] buffer, Session session)
See:
    https://docs.oracle.com/javaee/7/api/javax/websocket/OnMessage.html
*/
    @OnMessage
    public void onBinaryMessage(byte[] buffer, Session session) {
        try {
            System.out.println ("Binary message received.");
            FileOutputStream stream = new FileOutputStream(output_path);
            stream.write(buffer);
            stream.close();
            System.out.println ("Message contents written to file.");
            Close ();
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }

    @OnError
    public void onError(Session session, Throwable e) {
        System.out.println ("Error message received: " + e.getMessage ());
    }

    @OnClose
    public void myOnClose (CloseReason reason) {
        System.out.println ("Close message received: " + reason.getReasonPhrase());
    }

    public void SendAudio() {
        try {
            File file = new File(input_path);
            FileInputStream stream = new FileInputStream (file);
            byte buffer[] = new byte[(int)file.length()];
            stream.read(buffer);
            stream.close();
            sendMessage (buffer);
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }

    public void sendMessage(byte[] message) {
        try {
            System.out.println ("Sending audio.");
            OutputStream stream = this.session.getBasicRemote().getSendStream();
            stream.write (message);
/* Make sure the audio file is followed by silence.
 * This lets the service know that the audio file is finished.
 * At 32 bytes per millisecond, this is 10 seconds of silence. */
            System.out.println ("Sending silence.");
            byte silence[] = new byte[320000];
            stream.write (silence);
            stream.flush();
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }

    public void Connect () throws Exception {
        try {
            System.out.println("Connecting.");
            WebSocketContainer container = ContainerProvider.getWebSocketContainer();
/* The response message might exceed the default max message size of 65536. */
            container.setDefaultMaxBinaryMessageBufferSize(131072);
/* Some code samples show container.connectToServer as returning a Session, but this seems to be false. */
            container.connectToServer(this, new URI(uri));
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }

    public void Close () throws Exception {
        try {
            System.out.println("Closing connection.");
            this.session.close ();
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }
}
```

Speak.java：

```java
/*
Download javax.websocket-api-1.1.jar (or newer) from:
    http://central.maven.org/maven2/javax/websocket/javax.websocket-api/1.1/
Download jetty-http-9.4.11.v20180605.jar (or newer) from:
    https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-http
Download jetty-io-9.4.11.v20180605.jar (or newer) from:
    https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-io
Download jetty-util-9.4.11.v20180605.jar (or newer) from:
    https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-util
Download websocket-api-9.4.11.v20180605.jar (or newer) from:
    https://mvnrepository.com/artifact/org.eclipse.jetty.websocket/websocket-api
Download websocket-client-9.4.11.v20180605.jar (or newer) from:
    https://mvnrepository.com/artifact/org.eclipse.jetty.websocket/websocket-client
Download websocket-common-9.4.11.v20180605.jar (or newer) from:
    https://mvnrepository.com/artifact/org.eclipse.jetty.websocket/websocket-common
Download javax-websocket-client-impl-9.4.11.v20180605.jar (or newer) from:
    https://mvnrepository.com/artifact/org.eclipse.jetty.websocket/javax-websocket-client-impl
Download jetty-client-9.4.11.v20180605.jar (or newer) from:
    https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-client

Compile and run with:
    javac Config.java -cp .;javax.websocket-api-1.1.jar
    javac Client.java -cp .;javax.websocket-api-1.1.jar;javax-websocket-client-impl-9.4.11.v20180605.jar;websocket-common-9.4.11.v20180605.jar;jetty-util-9.4.11.v20180605.jar;jetty-io-9.4.11.v20180605.jar;websocket-api-9.4.11.v20180605.jar;websocket-client-9.4.11.v20180605.jar;jetty-client-9.4.11.v20180605.jar;jetty-http-9.4.11.v20180605.jar
    javac Speak.java -cp .;javax.websocket-api-1.1.jar;javax-websocket-client-impl-9.4.11.v20180605.jar;websocket-common-9.4.11.v20180605.jar;jetty-util-9.4.11.v20180605.jar;jetty-io-9.4.11.v20180605.jar;websocket-api-9.4.11.v20180605.jar;websocket-client-9.4.11.v20180605.jar;jetty-client-9.4.11.v20180605.jar;jetty-http-9.4.11.v20180605.jar
    java -cp .;javax.websocket-api-1.1.jar;javax-websocket-client-impl-9.4.11.v20180605.jar;websocket-common-9.4.11.v20180605.jar;jetty-util-9.4.11.v20180605.jar;jetty-io-9.4.11.v20180605.jar;websocket-api-9.4.11.v20180605.jar;websocket-client-9.4.11.v20180605.jar;jetty-client-9.4.11.v20180605.jar;jetty-http-9.4.11.v20180605.jar Speak
*/

import java.lang.Thread;

public class Speak {
    public static void main(String[] args) {
        try {
            Client client = new Client ();
            client.Connect ();
            // Wait for the reply.
            Thread.sleep (5000);
            System.out.println ("Press Enter to exit the application at any time.");
            System.in.read();
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

**翻译语音响应**

成功的结果是创建了名为“speak2.wav”的文件。 该文件包含“speak.wav”中口述内容的翻译。

[返回页首](#HOLTop)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [语音翻译教程](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>另请参阅 

[语音翻译概述](../overview.md)
[API 参考](http://docs.microsofttranslator.com/speech-translate.html)
