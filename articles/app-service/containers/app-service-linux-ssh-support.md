---
title: "对 Linux 上的 Azure 应用服务的 SSH 支持 | Microsoft Docs"
description: "了解如何将 SSH 与 Linux 上的 Azure 应用服务配合使用。"
keywords: "azure 应用服务, web 应用, linux, oss"
services: app-service
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: wesmc
ms.openlocfilehash: 7e6bb974565810ebb8d8e21d1c274d42d6d39e55
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2017
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>对 Linux 上的 Azure 应用服务的 SSH 支持

[安全外壳 (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) 是一种用于安全地使用网络服务的加密网络协议。 它通常用来从命令行安全地远程登录到某个系统并远程执行管理命令。

Linux 应用服务对应用容器提供了 SSH 支持，每个内置 Docker 映像都可用于新 Web 应用的运行时堆栈。

![运行时堆栈](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

还可以通过以下方式将 SSH 与自定义 Docker 映像配合使用：将 SSH 服务器包括为映像的一部分并按本主题中所述对其进行配置。

## <a name="making-a-client-connection"></a>建立客户端连接

若要建立 SSH 客户端连接，必须启动主站点。

使用以下形式将 Web 应用的源代码控制管理 (SCM) 终结点粘贴到浏览器中：

```bash
https://<your sitename>.scm.azurewebsites.net/webssh/host
```

如果尚未进行身份验证，则会要求向要连接的 Azure 订阅进行身份验证。

![SSH 连接](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="ssh-support-with-custom-docker-images"></a>对自定义 Docker 映像提供 SSH 支持

若要使自定义 Docker 映像支持在容器与 Azure 门户中的客户端之间进行 SSH 通信，必须针对 Docker 映像执行以下步骤。

作为[示例](https://github.com/Azure-App-Service/node/blob/master/6.9.3/)提供的 Azure 应用服务存储库中显示了这些步骤。

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

1. 向 Dockerfile 中添加一条 [`COPY` 指令](https://docs.docker.com/engine/reference/builder/#copy)以将 [sshd_config](http://man.openbsd.org/sshd_config) 文件复制到 */etc/ssh/* 目录中。 配置文件应当以[此处](https://github.com/Azure-App-Service/node/blob/master/8.2.1/sshd_config)的 Azure-App-Service GitHub 存储库中的 sshd_config 文件为基础。

    > [!NOTE]
    > *sshd_config* 文件必须包括以下项，否则连接会失败： 
    > * `Ciphers` 必须包括下列项中的至少一项：`aes128-cbc,3des-cbc,aes256-cbc`。
    > * `MACs` 必须包括下列项中的至少一项：`hmac-sha1,hmac-sha1-96`。

    ```docker
    COPY sshd_config /etc/ssh/
    ```

1. 在 Dockerfile 的 [`EXPOSE` 指令](https://docs.docker.com/engine/reference/builder/#expose)中包括端口 2222。 虽然根密码已知，但无法从 Internet 访问端口 2222。 它是一个仅供内部使用的端口，仅可供专用虚拟网络的桥网络中的容器访问。

    ```docker
    EXPOSE 2222 80
    ```

1. 请确保使用 */bin* 目录中的 shell 脚本[启动 ssh 服务](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh)。

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

请参阅以下链接，详细了解用于容器的 Web 应用。 如有问题和疑问，请在[我们的论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)上发帖。

* [如何对用于容器的 Web 应用使用自定义 Docker 映像](quickstart-custom-docker-image.md)
* [在 Linux 上的 Azure 应用服务中使用 .NET Core](quickstart-dotnetcore.md)
* [在 Linux 上的 Azure 应用服务中使用 Ruby](quickstart-ruby.md)
* [用于容器的 Azure 应用服务 Web 应用常见问题解答](app-service-linux-faq.md)