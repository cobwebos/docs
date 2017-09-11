---
title: "使用 C 将事件发送到 Azure 事件中心 | Microsoft 文档"
description: "使用 C 将事件发送到 Azure 事件中心"
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 08/15/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: a615ee39b6c3731cc7df366e9fabeed5219a71b4
ms.contentlocale: zh-cn
ms.lasthandoff: 08/16/2017

---

# <a name="send-events-to-azure-event-hubs-using-c"></a>使用 C 将事件发送到 Azure 事件中心

## <a name="introduction"></a>介绍
事件中心是一个具备高度伸缩性的引入系统，每秒可收入大量事件，从而使应用程序能够处理和分析连接的设备和应用程序所产生的海量数据。 将数据采集到事件中心后，可以使用任何实时分析提供程序或存储群集来转换和存储数据。

有关详细信息，请参阅 [事件中心概述][Event Hubs overview]。

在本教程中，将学习如何使用用 C 编写的控制台应用程序将事件发送到事件中心。要接收事件，请单击左侧目录中的相应接收语言。

若要完成本教程，需要满足以下条件：

* C 语言开发环境。 对于本教程，我们将假定 gcc 堆栈在使用 Ubuntu 14.04 的 Azure Linux 虚拟机上。
* [Microsoft Visual Studio](https://www.visualstudio.com/)。
* 有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="send-messages-to-event-hubs"></a>将消息发送到事件中心
在本部分中，我们将编写用于将事件发送到事件中心的 C 应用。 此代码使用 [Apache Qpid 项目](http://qpid.apache.org/)中的 Proton AMQP 库。 这类似于通过 C 将服务总线队列和主题与 AMQP 配合使用，如[此处](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504)所示。 有关详细信息，请参阅 [Qpid Proton 文档](http://qpid.apache.org/proton/index.html)。

1. 在 [Qpid AMQP Messenger 页](https://qpid.apache.org/proton/messenger.html)中，根据具体的环境，按照说明安装 Qpid Proton。
2. 若要编译 Proton 库，请安装以下程序包：
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. 下载 [Qpid Proton 库](http://qpid.apache.org/proton/index.html)并提取它，例如：
   
    ```shell
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
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
5. 在工作目录中，创建一个包含以下代码的名为 sender.c 的新文件。 请记得替换事件中心名称和命名空间名称的值。 还必须用密钥的 URL 编码版本替换之前创建的 **SendRule**。 可以在[此处](http://www.w3schools.com/tags/ref_urlencode.asp)对它进行 URL 编码。
   
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
   
    #define check(messenger)                                                     
      {                                                                          
        if(pn_messenger_errno(messenger))                                        
        {                                                                        
          printf("check\n");                                                     
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); 
        }                                                                        
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
        char * address = (char *) "amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/{event hub name}";
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
    > 在此代码中，我们使用传出窗口 1 以强制尽快发出消息。 通常，应用程序应尝试批处理消息，以提高吞吐量。 请参阅 [Qpid AMQP Messenger 页](https://qpid.apache.org/proton/messenger.html)，了解如何在此环境及其他环境中以及从为其提供了绑定的平台（目前为 Perl、PHP、Python 和 Ruby）中使用 Qpid Proton 库。


## <a name="next-steps"></a>后续步骤
访问以下链接可以了解有关事件中心的详细信息：

* [事件中心概述](event-hubs-what-is-event-hubs.md
)
* [创建事件中心](event-hubs-create.md)
* [事件中心常见问题](event-hubs-faq.md)

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

