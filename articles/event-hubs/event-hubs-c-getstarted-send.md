---
title: 使用 C 发送事件 - Azure 事件中心 | Microsoft Docs
description: 本文提供了创建 C 应用程序的演练，该应用程序用于将事件发送到 Azure 事件中心。
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: a8f647018ba6ed3c9e951db2054036b60c7d4ab5
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678762"
---
# <a name="send-events-to-azure-event-hubs-using-c"></a>使用 C 将事件发送到 Azure 事件中心

## <a name="introduction"></a>简介
Azure 事件中心是一个大数据流式处理平台和事件引入服务，每秒能够接收和处理数百万个事件。 事件中心可以处理和存储分布式软件和设备生成的事件、数据或遥测。 可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到数据中心的数据。 有关事件中心的详细概述，请参阅[事件中心概述](event-hubs-about.md)和[事件中心功能](event-hubs-features.md)。

本教程介绍如何使用用 C 编写的控制台应用程序将事件发送到事件中心。 

## <a name="prerequisites"></a>必备组件
要完成本教程，需要以下各项：

* C 语言开发环境。 本教程假定 gcc 堆栈在使用 Ubuntu 14.04 的 Azure Linux VM 上。
* [Microsoft Visual Studio](https://www.visualstudio.com/)。
* **创建事件中心命名空间和事件中心**。 使用[Azure 门户](https://portal.azure.com)创建事件中心类型的命名空间并获取你的应用程序与事件中心进行通信所需的管理凭据。 要创建命名空间和事件中心，请按照[此文](event-hubs-create.md)中的步骤操作。 按照以下文章中的说明获取事件中心访问密钥的值：[获取连接字符串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 可在本教程后面编写的代码中使用该访问密钥。 默认密钥名称为：RootManageSharedAccessKey。

## <a name="write-code-to-send-messages-to-event-hubs"></a>编写将消息发送到事件中心的代码
本部分介绍如何编写用于将事件发送到事件中心的 C 应用。 此代码使用 [Apache Qpid 项目](https://qpid.apache.org/)中的 Proton AMQP 库。 这类似于通过 C 将服务总线队列和主题与 AMQP 配合使用，如[此示例](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504)所示。 有关详细信息，请参阅 [Qpid Proton 文档](https://qpid.apache.org/proton/index.html)。

1. 在 [Qpid AMQP Messenger 页](https://qpid.apache.org/proton/messenger.html)中，根据具体的环境，按照说明安装 Qpid Proton。
2. 若要编译 Proton 库，请安装以下程序包：
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. 下载 [Qpid Proton 库](https://qpid.apache.org/proton/index.html)并提取它，例如：
   
    ```shell
    wget https://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. 创建生成目录、编译和安装：
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. 在工作目录中，创建一个包含以下代码的名为 sender.c 的新文件。 请记得替换为你的 SAS 密钥/名称、事件中心名称和命名空间的值。 还必须用密钥的 URL 编码版本替换之前创建的 **SendRule**。 可以在[此处](https://www.w3schools.com/tags/ref_urlencode.asp)对它进行 URL 编码。
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. 使用 **gcc** 编译该文件：
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > 此代码使用传出窗口 1 以强制尽快发出消息。 建议应用程序尝试批处理消息，以提高吞吐量。 请参阅 [Qpid AMQP Messenger 页](https://qpid.apache.org/proton/messenger.html)，了解如何在此环境及其他环境中以及从为其提供了绑定的平台（目前为 Perl、PHP、Python 和 Ruby）中使用 Qpid Proton 库。

运行应用程序将消息发送到事件中心。 

祝贺你！ 现在已向事件中心发送消息。

## <a name="next-steps"></a>后续步骤
请阅读以下文章：

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [功能和 Azure 事件中心内的术语](event-hubs-features.md)。


<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
