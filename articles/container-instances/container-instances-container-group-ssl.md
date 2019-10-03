---
title: 在 Azure 容器实例中启用 SSL
description: 为 Azure 容器实例中运行的容器组创建 SSL 或 TLS 终结点
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: f11fb209f8d2ace51081fd81f453faf9505af27c
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326071"
---
# <a name="enable-an-ssl-endpoint-in-a-container-group"></a>在容器组中启用 SSL 终结点

本文介绍如何使用应用程序容器和运行 SSL 提供程序的挎斗容器创建[容器组](container-instances-container-groups.md)。 通过使用单独的 SSL 终结点设置容器组, 你可以为应用程序启用 SSL 连接, 而无需更改应用程序代码。

设置包含两个容器的容器组:
* 使用公共 Microsoft [helloworld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld)映像运行简单 web 应用的应用程序容器。 
* 运行公共[Nginx](https://hub.docker.com/_/nginx)映像的挎斗容器, 配置为使用 SSL。 

在此示例中, 容器组只公开端口443的 Nginx 及其公共 IP 地址。 Nginx 将 HTTPS 请求路由到辅助 web 应用, 该应用在内部侦听端口80。 可以调整侦听其他端口的容器应用的示例。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

可以使用 Azure Cloud Shell 或 Azure CLI 的本地安装完成本文的内容。 如果想要在本地使用它，建议使用 2.0.55 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-self-signed-certificate"></a>创建自签名证书

若要将 Nginx 设置为 SSL 提供程序, 需要 SSL 证书。 本文介绍如何创建和设置自签名 SSL 证书。 对于生产方案, 应从证书颁发机构获取证书。

若要创建自签名 SSL 证书, 请使用 Azure Cloud Shell 和许多 Linux 发行版中提供的[OpenSSL](https://www.openssl.org/)工具, 或在操作系统中使用类似的客户端工具。

首先在本地工作目录中创建证书请求 (csr 文件):

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

按照提示添加标识信息。 对于 "公用名", 请输入与证书关联的主机名。 当系统提示输入密码时, 无需键入即可按 Enter, 以跳过添加密码。

运行以下命令, 从证书请求创建自签名证书 (.crt 文件)。 例如：

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

你现在应会在目录中看到三个文件: 证书请求`ssl.csr`()、私钥 (`ssl.key`) 和自签名证书 (`ssl.crt`)。 在后面`ssl.key`的`ssl.crt`步骤中使用和。

## <a name="configure-nginx-to-use-ssl"></a>将 Nginx 配置为使用 SSL

### <a name="create-nginx-configuration-file"></a>创建 Nginx 配置文件

在本部分中, 将创建 Nginx 的配置文件以使用 SSL。 首先, 将以下文本复制到名为`nginx.conf`的新文件中。 在 Azure Cloud Shell 中, 可以使用 Visual Studio Code 在工作目录中创建文件:

```console
code nginx.conf
```

在`location`中, 请确保为`proxy_pass`应用设置正确的端口。 在此示例中, 我们为容器设置了`aci-helloworld`端口80。

```console
# nginx Configuration File
# https://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1 TLSv1.1 TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;


        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80
            
            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>Base64-编码机密和配置文件

Base64-对 Nginx 配置文件、SSL 证书和 SSL 密钥进行编码。 在下一部分中, 将在用于部署容器组的 YAML 文件中输入编码内容。

```console
cat nginx.conf | base64 -w 0 > base64-nginx.conf
cat ssl.crt | base64 -w 0 > base64-ssl.crt
cat ssl.key | base64 -w 0 > base64-ssl.key
```

## <a name="deploy-container-group"></a>部署容器组

现在, 通过在[YAML 文件](container-instances-multi-container-yaml.md)中指定容器配置来部署容器组。

### <a name="create-yaml-file"></a>创建 YAML 文件

将以下 YAML 复制到名为`deploy-aci.yaml`的新文件中。 在 Azure Cloud Shell 中, 可以使用 Visual Studio Code 在工作目录中创建文件:

```console
code deploy-aci.yaml
```

输入 base64 编码文件的内容, 其中指定`secret`了。 例如, `cat`每个 base64 编码的文件都可查看其内容。 在部署过程中, 这些文件将被添加到容器组中的[机密卷](container-instances-volume-secret.md)。 在此示例中, 机密卷装载到 Nginx 容器中。

```YAML
api-version: 2018-10-01
location: westus
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: nginx
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <Enter contents of base64-ssl.crt here>
      ssl.key: <Enter contents of base64-ssl.key here>
      nginx.conf: <Enter contents of base64-nginx.conf here>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>部署容器组

使用[az group create](/cli/azure/group#az-group-create)命令创建资源组:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用[az container create](/cli/azure/container#az-container-create)命令部署容器组, 并将 YAML 文件作为参数传递。

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>查看部署状态

若要查看部署状态, 请使用以下[az container show](/cli/azure/container#az-container-show)命令:

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

对于成功的部署, 输出类似于以下内容:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   mcr.microsoft.com/azuredocs/nginx, aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-ssl-connection"></a>验证 SSL 连接

若要查看正在运行的应用程序, 请在浏览器中导航到其 IP 地址。 例如, 此示例中显示的 IP 地址是`52.157.22.76`。 由于 Nginx 服务器`https://<IP-ADDRESS>`配置的原因, 您必须使用查看正在运行的应用程序。 尝试连接`http://<IP-ADDRESS>`失败。

![浏览器屏幕截图，显示应用程序在 Azure 容器实例中运行](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> 由于本示例使用自签名证书, 而不是证书颁发机构颁发的证书, 因此在通过 HTTPS 连接到站点时, 浏览器会显示安全警告。 此行为是预期的行为。
>

## <a name="next-steps"></a>后续步骤

本文介绍如何设置 Nginx 容器, 以启用到容器组中运行的 web 应用的 SSL 连接。 对于侦听端口80以外的端口的应用, 你可以调整此示例。 还可以更新 Nginx 配置文件, 以自动重定向端口 80 (HTTP) 上的服务器连接以使用 HTTPS。

尽管本文在挎斗中使用 Nginx, 但你可以使用另一个 SSL 提供程序, 例如[Caddy](https://caddyserver.com/)。

在容器组中启用 SSL 的另一种方法是在[azure 虚拟网络](container-instances-vnet.md)中使用[azure 应用程序网关](../application-gateway/overview.md)部署组。 网关可以设置为 SSL 终结点。 请参阅可调整的示例[部署模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet), 以便在网关上启用 SSL 终端。
