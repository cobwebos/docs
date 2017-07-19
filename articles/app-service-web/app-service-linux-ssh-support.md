---
title: "对 Linux 上的 Azure 应用服务 Web 应用的 SSH 支持 | Microsoft Docs"
description: "了解如何将 SSH 与 Linux 上的 Azure Web 应用配合使用。"
keywords: "azure 应用服务, web 应用, linux, oss"
services: app-service
documentationcenter: 
author: wesmc7777
manager: erikre
editor: 
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 6da663ea282e09b01ce380827fa7e31505712516
ms.contentlocale: zh-cn
ms.lasthandoff: 07/04/2017


---
# <a name="ssh-support-for-azure-web-app-on-linux"></a>对 Linux 上的 Azure Web 应用的 SSH 支持

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="overview"></a>概述

[安全外壳 (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) 是一种用于安全地使用网络服务的加密网络协议。 它通常用来从命令行安全地远程登录到某个系统并远程执行管理命令。

Linux 上的 Web 应用程序对应用程序容器提供了 SSH 支持，每个内置 Docker 映像都可用于新 Web 应用程序的运行时堆栈。 

![运行时堆栈](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

你还可以通过以下方式将 SSH 与自定义 Docker 映像配合使用：将 SSH 服务器包括为映像的一部分并按本主题中所述对其进行配置。



## <a name="making-a-client-connection"></a>建立客户端连接

若要建立 SSH 客户端连接，必须启动主站点。 

使用以下形式将你的 Web 应用的源代码控制管理 (SCM) 终结点粘贴到浏览器中：

        https://<your sitename>.scm.azurewebsites.net/webssh/host

如果你尚未进行身份验证，则会要求你向要连接的 Azure 订阅进行身份验证。

![SSH 连接](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)


## <a name="ssh-support-with-custom-docker-images"></a>对自定义 Docker 映像提供 SSH 支持

若要使自定义 Docker 映像支持在容器与 Azure 门户中的客户端之间进行 SSH 通信，必须针对 Docker 映像执行以下步骤。 

[此处](https://github.com/Azure-App-Service/node/tree/master/4.4.7-1)作为示例提供的 Azure 应用服务存储库中显示了这些步骤。

1. 在映像的 Dockerfile 中的 [`RUN` 指令](https://docs.docker.com/engine/reference/builder/#run)中包括 `openssh-server` 安装，并将根帐户的密码设置为 `"Docker!"`。 

    > [!NOTE] 
    > 此配置不允许从外部建立到容器的连接。 只能通过 Kudu / SCM 站点访问 SSH，该站点使用发布凭据进行身份验证。

    ```docker
    # ------------------------
    # SSH Server support
    # ------------------------
    RUN apt-get update \ 
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "root:Docker!" | chpasswd
    ``` 

2. 向 Dockerfile 中添加一条 [`COPY` 指令](https://docs.docker.com/engine/reference/builder/#copy)以将 [sshd_config](http://man.openbsd.org/sshd_config) 文件复制到 */etc/ssh/* 目录中。 你的配置文件应当以[此处](https://github.com/Azure-App-Service/node/blob/master/6.11/sshd_config)的 Azure-App-Service GitHub 存储库中的 sshd_config 文件为基础。

    > [!NOTE] 
    > *sshd_config* 文件必须包括以下项，否则连接将失败： 
    > * `Ciphers` 必须包括下列项中的至少一项：`aes128-cbc,3des-cbc,aes256-cbc`。
    > * `MACs` 必须包括下列项中的至少一项：`hmac-sha1,hmac-sha1-96`。

    ```docker
    COPY sshd_config /etc/ssh/
    ```


3. 在 Dockerfile 的 [`EXPOSE` 指令](https://docs.docker.com/engine/reference/builder/#expose)中包括端口 2222。 虽然根密码已知，但无法从 Internet 访问端口 2222。 它是一个仅供内部使用的端口，仅可供专用虚拟网络的桥网络中的容器访问。

    ```docker
    EXPOSE 2222 80
    ```

4. 确保启动 ssh 服务。 [此处](https://github.com/Azure-App-Service/node/blob/master/6.9.3-1/init_container.sh)的示例使用了 */bin* 目录中的一个 shell 脚本。

    ```bash
    #!/bin/bash
    service ssh start
    ```

    Dockerfile 使用 [`CMD` 指令](https://docs.docker.com/engine/reference/builder/#cmd)来运行该脚本。

    ```docker
    COPY init_container.sh /bin/
      ...
    RUN chmod 755 /bin/init_container.sh 
      ...       
    CMD ["/bin/init_container.sh"]
    ```



## <a name="next-steps"></a>后续步骤
有关 Linux 上的 Web 应用的详细信息，请参阅以下链接。 如有问题和疑问，请在[我们的论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)上发帖。

* [在 Linux 上的 Azure Web 应用中创建应用](app-service-linux-how-to-create-web-app.md)
* [如何将自定义 Docker 映像用于 Linux 上的 Azure Web 应用](app-service-linux-using-custom-docker-image.md)
* [在 Linux 上的 Azure Web 应用中使用针对 Node.js 的 PM2 配置](app-service-linux-using-nodejs-pm2.md)
* [在 Linux 上的 Azure Web 应用中使用 .NET Core](app-service-linux-using-dotnetcore.md)
* [在 Linux 上的 Azure Web 应用中使用 Ruby](app-service-linux-ruby-get-started.md)
* [Linux 上的 Azure 应用服务 Web 应用常见问题解答](app-service-linux-faq.md)


