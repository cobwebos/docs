---
title: 对 Linux 上的 Azure 应用服务的 SSH 支持 | Microsoft Docs
description: 了解如何将 SSH 与 Linux 上的 Azure 应用服务配合使用。
keywords: azure 应用服务, web 应用, linux, oss
services: app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: wesmc
ms.openlocfilehash: 63fdf2cc82438fe55792b12244dd697721adda15
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579572"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>对 Linux 上的 Azure 应用服务的 SSH 支持

[安全外壳 (SSH)](https://wikipedia.org/wiki/Secure_Shell) 通常用于通过命令行终端以远程方式执行管理命令。 Linux 应用服务对应用容器提供了 SSH 支持，每个内置 Docker 映像都可用于新 Web 应用的运行时堆栈。 

![运行时堆栈](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

对于自定义 Docker 映像，可以在自定义映像中配置 SSH 服务器。

还可以使用 SSH 和 SFTP 直接从本地开发计算机连接到容器。

## <a name="open-ssh-session-in-browser"></a>在浏览器中打开 SSH 会话

若要建立与容器的 SSH 客户端连接，应用应该处于正在运行状态。

将以下 URL 粘贴到浏览器中，将 \<app_name> 替换为应用名称：

```
https://<app_name>.scm.azurewebsites.net/webssh/host
```

如果尚未进行身份验证，则会要求向要连接的 Azure 订阅进行身份验证。 完成身份验证以后，可以看到一个浏览器内 shell，可以在其中的容器中运行命令。

![SSH 连接](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="use-ssh-support-with-custom-docker-images"></a>将 SSH 支持与自定义 Docker 映像配合使用

若要使自定义 Docker 映像支持在容器与 Azure 门户中的客户端之间进行 SSH 通信，必须针对 Docker 映像执行以下步骤。

这些步骤作为[示例](https://github.com/Azure-App-Service/node/blob/master/6.9.3/)显示在 Azure 应用服务存储库中。

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

1. 请确保使用 shell 脚本启动 SSH 服务（请参见 [init_container.sh](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh) 中的示例）。

    ```bash
    #!/bin/bash
    service ssh start
    ```

Dockerfile 使用 [`ENTRYPOINT` 指令](https://docs.docker.com/engine/reference/builder/#entrypoint)来运行该脚本。

    ```docker
    COPY startup /opt/startup
    ...
    RUN chmod 755 /opt/startup/init_container.sh
    ...
    ENTRYPOINT ["/opt/startup/init_container.sh"]
    ```

## <a name="open-ssh-session-from-remote-shell"></a>从远程 shell 打开 SSH 会话

> [!NOTE]
> 此功能目前为预览版。
>

使用 TCP 隧道，可以通过经身份验证的 WebSocket 连接在开发计算机与适用于容器的 Web 应用之间创建网络连接。 这样就可以通过所选客户端使用在应用服务中运行的容器打开一个 SSH 会话。

若要开始，需安装 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。 若要了解它在不安装 Azure CLI 的情况下的工作方式，请打开 [Azure Cloud Shell](../../cloud-shell/overview.md)。 

通过运行 [az extension add](/cli/azure/extension?view=azure-cli-latest#az-extension-add) 来添加最新的应用服务扩展：

```azurecli-interactive
az extension add --name webapp
```

如果此前已运行 `az extension add`，请改为运行 [az extension update](/cli/azure/extension?view=azure-cli-latest#az-extension-update)：

```azurecli-interactive
az extension update --name webapp
```

使用 [az webapp remote-connection create](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) 命令打开到应用的远程连接。 为应用指定 \<subscription\_id>、\<group\_name> 和 <app\_name>，将 \<port> 替换为本地端口号\_。

```azurecli-interactive
az webapp remote-connection create --subscription <subscription_id> --resource-group <group_name> -n <app_name> -p <port> &
```

> [!TIP]
> 命令末尾的 `&` 只是为了方便起见（如果你是使用 Cloud Shell）。 它在后台运行此进程，因此你可以在同一 shell 中运行下一命令。

命令输出提供的信息是打开 SSH 会话所需的。

```
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

请使用本地端口通过所选客户端打开容器的 SSH 会话。 以下示例使用默认的 [ssh](https://ss64.com/bash/ssh.html) 命令：

```azurecli-interactive
ssh root@127.0.0.1 -p <port>
```

出现提示时，请键入 `yes` 继续进行连接。 然后，系统会提示输入密码。 请使用 `Docker!`（此前已向你显示过）。

```
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

进行身份验证以后，会看到会话欢迎屏幕。

```
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  >
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
```

现在已连接到连接器。 

请尝试运行 [top](https://ss64.com/bash/top.html) 命令。 应该可以在进程列表中看到应用的进程。 在下面的示例输出中，它带有 `PID 263`。

```
Mem: 1578756K used, 127032K free, 8744K shrd, 201592K buff, 341348K cached
CPU:   3% usr   3% sys   0% nic  92% idle   0% io   0% irq   0% sirq
Load average: 0.07 0.04 0.08 4/765 45738
  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    1     0 root     S     1528   0%   0   0% /sbin/init
  235     1 root     S     632m  38%   0   0% PM2 v2.10.3: God Daemon (/root/.pm2)
  263   235 root     S     630m  38%   0   0% node /home/site/wwwroot/app.js
  482   291 root     S     7368   0%   0   0% sshd: root@pts/0
45513   291 root     S     7356   0%   0   0% sshd: root@pts/1
  291     1 root     S     7324   0%   0   0% /usr/sbin/sshd
  490   482 root     S     1540   0%   0   0% -ash
45539 45513 root     S     1540   0%   0   0% -ash
45678 45539 root     R     1536   0%   0   0% top
45733     1 root     Z        0   0%   0   0% [init]
45734     1 root     Z        0   0%   0   0% [init]
45735     1 root     Z        0   0%   0   0% [init]
45736     1 root     Z        0   0%   0   0% [init]
45737     1 root     Z        0   0%   0   0% [init]
45738     1 root     Z        0   0%   0   0% [init]
```

## <a name="next-steps"></a>后续步骤

如有问题和疑问，请在 [Azure 论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)上发帖。

有关用于容器的 Web 应用的详细信息，请参阅：

* [Introducing remote debugging of Node.js apps on Azure App Service from VS Code](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)（使用 VS Code 对 Azure 应用服务上的 Node.js 应用进行远程调试简介）
* [如何对用于容器的 Web 应用使用自定义 Docker 映像](quickstart-docker-go.md)
* [在 Linux 上的 Azure 应用服务中使用 .NET Core](quickstart-dotnetcore.md)
* [在 Linux 上的 Azure 应用服务中使用 Ruby](quickstart-ruby.md)
* [用于容器的 Azure 应用服务 Web 应用常见问题解答](app-service-linux-faq.md)
