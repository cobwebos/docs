---
title: Azure 容器注册表-方面的常见问题
description: 有关与 Azure 容器注册表服务相关的常见问题的答案
services: container-registry
author: sajayantony
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 5/13/2019
ms.author: sajaya
ms.openlocfilehash: beeb4986750e398071e3afb6c1f04663f858cec1
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303580"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>有关 Azure 容器注册表常见问题

本文介绍一些常见问题和了解 Azure 容器注册表的已知的问题。

## <a name="resource-management"></a>资源管理

- [可以创建使用 Resource Manager 模板的 Azure 容器注册表？](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [是否有安全漏洞扫描的 ACR 中映像？](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [如何使用 Azure 容器注册表配置 Kubernetes？](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [如何为容器注册表获取管理员凭据？](#how-do-i-get-admin-credentials-for-a-container-registry)
- [如何获取资源管理器模板中的管理员凭据？](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [尽管使用 Azure CLI 或 Azure PowerShell 获取删除复制的复制的删除失败并显示禁止访问状态](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>可以创建 Azure 容器注册表使用资源管理器模板？

是的。 下面是[模板](https://github.com/Azure/azure-cli/blob/master/src/command_modules/azure-cli-acr/azure/cli/command_modules/acr/template.json)可用于创建注册表。

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>是否有安全漏洞扫描的 ACR 中映像？

是的。 请参阅的文档[Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/)并[Aqua](http://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry)。

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>如何使用 Azure 容器注册表配置 Kubernetes？

请参阅的文档[Kubernetes](http://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr)以及步骤[Azure Kubernetes 服务](container-registry-auth-aks.md)。

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>如何为容器注册表获取管理员凭据？

> [!IMPORTANT]
> 管理员用户帐户专门用于单个用户访问注册表，主要用于测试目的。 建议不要与多个用户共享管理员帐户凭据。 建议用户和服务主体在无外设方案中使用单个标识。 请参阅[身份验证概述](container-registry-authentication.md)。

管理员凭据之前，请确保启用注册表的管理员用户。

若要获取使用 Azure CLI 的凭据：

```azurecli
az acr credential show -n myRegistry
```

使用 Azure Powershell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>如何获取资源管理器模板中的管理员凭据？

> [!IMPORTANT]
> 管理员用户帐户专门用于单个用户访问注册表，主要用于测试目的。 建议不要与多个用户共享管理员帐户凭据。 建议用户和服务主体在无外设方案中使用单个标识。 请参阅[身份验证概述](container-registry-authentication.md)。

管理员凭据之前，请确保启用注册表的管理员用户。

若要获取第一个密码：

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

若要获取第二个密码：

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>尽管使用 Azure CLI 或 Azure PowerShell 获取删除复制的复制的删除失败并显示禁止访问状态

当用户对注册表具有权限，但不会对订阅具有读取器级别的权限，则会出现错误。 若要解决此问题，请向该用户分配订阅的读取器权限：


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

## <a name="registry-operations"></a>注册表操作

- [如何访问 Docker 注册表 HTTP API V2？](#how-do-i-access-docker-registry-http-api-v2)
- [如何删除未引用的存储库中的任一标记的所有清单？](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [为什么不会注册表配额使用情况减少后删除映像？](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [如何验证存储配额更改？](#how-do-i-validate-storage-quota-changes)
- [如何我进行身份验证使用我注册表时在容器中运行 CLI？](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Azure 容器注册表确实提供了 TLS v1.2 唯一配置以及如何启用 TLS v1.2？](#does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12)
- [Azure 容器注册表支持内容信任？](#does-azure-container-registry-support-content-trust)
- [如何授予访问请求或推送映像，而无需管理注册表资源的权限？](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [如何启用注册表的自动映像隔离区](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>如何访问 Docker 注册表 HTTP API V2？

ACR 支持 Docker 注册表 HTTP API V2。 Api 可以访问在`https://<your registry login server>/v2/`。 示例： `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>如何删除未引用的存储库中的任一标记的所有清单？

如果在 bash 中：

```bash
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

对于 Powershell:

```powershell
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

请注意:您可以添加`-y`中要跳过确认的 delete 命令。

有关详细信息，请参阅[删除 Azure 容器注册表中的容器映像](container-registry-delete.md)。

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>为什么不会注册表配额使用情况减少后删除映像？

如果基础层仍被引用的其他容器映像，则可能发生这种情况。 如果删除且不引用映像，在几分钟内将更新注册表使用情况。

### <a name="how-do-i-validate-storage-quota-changes"></a>如何验证存储配额更改？

使用 1 GB 层使用以下 docker 文件创建映像。 这可确保图像有未由任何其他映像在注册表中共享的层。

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

生成并将映像推送到注册表使用 docker CLI。

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

您应能够看到存储使用量已增加在 Azure 门户中，也可以查询使用 CLI 的使用情况。

```bash
az acr show-usage -n myregistry
```

删除使用 Azure CLI 或门户的映像，并在几分钟后检查更新的使用情况。

```bash
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>如何我进行身份验证使用我注册表时在容器中运行 CLI？

需要运行 Azure CLI 容器装载 Docker 套接字：

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

在容器中安装`docker`:

```bash
apk --update add docker
```

与注册表进行身份验证：

```azurecli
az acr login -n MyRegistry
```

### <a name="does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12"></a>Azure 容器注册表确实提供了 TLS v1.2 唯一配置以及如何启用 TLS v1.2？

是的。 使用任何最新的 docker 客户端启用 TLS (版本 18.03.0 及更高版本)。 

### <a name="does-azure-container-registry-support-content-trust"></a>Azure 容器注册表支持内容信任？

是的您可以使用受信任的映像在 Azure 容器注册表中，由于[Docker 公证人](https://docs.docker.com/notary/getting_started/)进行了集成并可启用。 有关详细信息，请参阅[Azure 容器注册表中的内容信任](container-registry-content-trust.md)。


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>指纹位于该文件位于何处？

下`~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* 公共密钥和证书 （除了委派角色） 的所有角色都存储在`root.json`。
* 公钥和委派角色的证书存储在其父作用的 JSON 文件中 (例如`targets.json`为`targets/releases`角色)。

它会建议，以通过 Docker 和公证人客户端的总体 TUF 验证后，验证这些公共密钥和证书。

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>如何授予访问请求或推送映像，而无需管理注册表资源的权限？

支持 ACR[自定义角色](container-registry-roles.md)提供不同级别的权限。 具体而言，`AcrPull`和`AcrPush`角色允许用户请求和/或推送映像，而无需管理 Azure 中的注册表资源的权限。

* Azure 门户：注册表-> 访问控制 (IAM)-> 添加 (选择`AcrPull`或`AcrPush`角色)。
* Azure CLI：通过运行以下命令查找注册表的资源 ID:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  然后可以将分配`AcrPull`或`AcrPush`向用户角色 (下面的示例使用`AcrPull`):

  ```azurecli
    az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
    ```

  或者，将角色分配给由其应用程序 ID 标识的服务原则：

  ```
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

受托人便能进行身份验证和访问注册表中的映像。

* 若要对注册表进行身份验证：
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* 列出存储库：

  ```azurecli
  az acr repository list -n myRegistry
  ```

 拉取映像：
    
  ```azurecli
  docker pull myregistry.azurecr.io/hello-world
  ```

通过使用唯一`AcrPull`或`AcrPush`角色，受托人不具备的权限来管理 Azure 中的注册表资源。 例如，`az acr list`或`az acr show -n myRegistry`不会显示注册表。

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>如何启用注册表的自动映像隔离区？

映像隔离当前是一项预览功能的 ACR。 可以启用注册表的隔离模式，以便只有这些映像已成功传递安全扫描的正常用户均可见。 有关详细信息，请参阅[ACR GitHub 存储库](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)。

## <a name="diagnostics"></a>诊断

- [docker 拉取失败，出现错误： net/http： 等待连接 (Client.Timeout 时等待标头超出了) 时取消请求](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [docker 推送成功，但 docker 拉取失败，出现错误： 未授权： 所需的身份验证](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [启用并获取 docker 守护程序的调试日志](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [新的用户权限不可能在更新后立即生效](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [未上直接的 REST API 调用的正确格式提供身份验证信息](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [为什么不会在 Azure 门户列出我的存储库或标记？](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [如何在 Windows 上收集 http 跟踪？](#how-do-i-collect-http-traces-on-windows)

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>docker 拉取失败，出现错误： net/http： 等待连接 (Client.Timeout 时等待标头超出了) 时取消请求

 - 如果此错误是暂时性问题，然后重试将会成功。
 - 如果`docker pull`连续失败，则可能有 docker 守护程序的问题。 重新启动 docker 守护程序，通常可以缓解此问题。 
 - 如果继续遇到此问题，重新启动 docker 守护程序后，问题可能是计算机的一些网络连接问题。 要查看在计算机上的常规网络是否正常运行，请尝试命令如`ping www.bing.com`。
 - Docker 客户端的所有操作，应始终具有重试机制。

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>docker 推送成功，但 docker 拉取失败，出现错误： 未授权： 所需的身份验证

使用 Red Hat 版本的 docker 守护程序，则会发生此错误在其中`--signature-verification`默认情况下启用。 可以通过运行以下命令检查 Red Hat Enterprise Linux (RHEL) 或 Fedora 的 docker 守护程序选项：

```bash
grep OPTIONS /etc/sysconfig/docker
```

例如，Fedora 28 服务器具有以下 docker 守护程序选项：

```
OPTIONS='--selinux-enabled --log-driver=journald --live-restore'
```

与`--signature-verification=false`缺失，`docker pull`失败，出现如下错误：

```bash
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

解决该错误：
1. 将选项添加`--signature-verification=false`docker 守护程序配置文件到`/etc/sysconfig/docker`。 例如：

  ```
  OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'
  ```
2. 通过运行以下命令重新启动 docker 后台程序服务：

  ```bash
  sudo systemctl restart docker.service
  ```

详细信息`--signature-verification`可以通过运行找到`man dockerd`。

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>启用并获取 docker 守护程序的调试日志  

启动`dockerd`与`debug`选项。 首先，创建 docker 守护程序配置文件 (`/etc/docker/daemon.json`) 如果不存在，并添加`debug`选项：

```json
{   
    "debug": true   
}
```

然后，重新启动该守护程序。 例如，使用 Ubuntu 14.04:

```bash
sudo service docker restart
```

可以在中找到详细信息[Docker 文档](https://docs.docker.com/engine/admin/#enable-debugging)。 

 * 可能在不同的位置，具体取决于您的系统生成日志。 例如，对于 Ubuntu 14.04，它具有`/var/log/upstart/docker.log`。   
请参阅[Docker 文档](https://docs.docker.com/engine/admin/#read-the-logs)有关详细信息。    

 * 对于 Windows Docker %localappdata%/docker/ 下生成日志。 但是它可能不包含所有调试信息尚未。   

   若要访问完整的守护程序日志，你可能需要一些额外的步骤：

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    现在你有权访问的 VM 正在运行的所有文件`dockerd`。 日志位于`/var/log/docker.log`。

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>新的用户权限不可能在更新后立即生效

当新授予权限 （新角色） 向服务主体，更改可能不会立即生效。 有两个可能的原因：

* Azure Active Directory 角色分配延迟。 通常情况下速度快，但可能需要由于传播延迟分钟。
* ACR 令牌服务器上的权限延迟。 这可能需要最多 10 分钟。 若要缓解问题，您可以`docker logout`，然后再次执行身份验证具有相同的用户在 1 分钟后：

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

当前 ACR 不支持通过用户的主复制删除。 解决方法是包括主复制在模板中创建，但添加中跳过创建`"condition": false`，如下所示：

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>未上直接的 REST API 调用的正确格式提供身份验证信息

你可能会遇到`InvalidAuthenticationInfo`错误，尤其使用`curl`工具的选项`-L`， `--location` （若要遵循重定向）。
例如，提取 blob 使用`curl`与`-L`选项和基本身份验证：

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

可能会导致以下响应：

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

根本原因是某些`curl`实现遵循从原始请求标头使用的重定向。

若要解决此问题，需要遵循重定向手动不带标头。 打印的响应标头`-D -`的选项`curl`然后提取：`Location`标头：

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>为什么不会在 Azure 门户列出我的存储库或标记？ 

如果使用 Microsoft Edge 浏览器，可以看到最多 100 存储库或列出的标记。 如果注册表具有 100 多个存储库或标记，我们建议使用 Firefox 或 Chrome 浏览以全部列出这些。

### <a name="how-do-i-collect-http-traces-on-windows"></a>如何在 Windows 上收集 http 跟踪？

#### <a name="prerequisites"></a>必备组件

- 启用解密 https 在 fiddler 中：  <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- 启用 Docker 若要使用的代理通过 Docker ui: <https://docs.docker.com/docker-for-windows/#proxies>
- 请确保还原完成后。  启用此功能不起作用 docker 和 fiddler 未运行。

#### <a name="windows-containers"></a>Windows 容器

配置 Docker 代理到 127.0.0.1: 8888

#### <a name="linux-containers"></a>Linux 容器

查找 vm 的虚拟交换机的 Docker ip:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

配置 Docker 代理输出的前一命令和端口 8888 (例如 10.0.75.1:8888)

## <a name="tasks"></a>任务

- [如何批处理取消运行？](#how-do-i-batch-cancel-runs)
- [如何在 az acr build 命令中包括.git 文件夹？](#how-do-i-include-the-git-folder-in-az-acr-build-command)

### <a name="how-do-i-batch-cancel-runs"></a>如何批处理取消运行？

以下命令取消所有正在运行的任务中指定的注册表。

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>如何在 az acr build 命令中包括.git 文件夹？

如果传递到的本地源文件夹`az acr build`命令，`.git`从已上传包默认情况下排除文件夹。 您可以创建`.dockerignore`文件使用以下设置。 它告诉命令还原下的所有文件`.git`上载的包中。 

```
!.git/**
```

此设置也适用于`az acr run`命令。

## <a name="cicd-integration"></a>CI/CD 集成

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub 操作](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="error-references-for-az-acr-check-health"></a>错误引用 `az acr check-health`

### <a name="dockercommanderror"></a>DOCKER_COMMAND_ERROR

此错误意味着该 docker 客户端的 CLI 未找到，它可以阻止查找 docker 版本、 评估 docker 守护程序状态和确保可以运行 docker pull 命令。

*可能的解决方案*： 安装 docker 客户端; 添加 docker 路径的系统变量。

### <a name="dockerdaemonerror"></a>DOCKER_DAEMON_ERROR

此错误意味着 docker 守护程序状态为不可用，或者，它无法访问使用 CLI。 这意味着将无法通过 CLI docker 操作 （例如，登录名、 请求）。

*可能的解决方案*:重新启动 docker 守护程序，或验证已正确安装。

### <a name="dockerversionerror"></a>DOCKER_VERSION_ERROR

此错误表示 CLI 程序无法运行该命令`docker --version`。

*可能的解决方案*： 尝试手动运行命令，请确保使用最新的 CLI 版本，并调查的错误消息。

### <a name="dockerpullerror"></a>DOCKER_PULL_ERROR

此错误表示 CLI 不能对您的环境实现的示例图像。

*可能的解决方案*： 验证拉取映像所需的所有组件都正常都运行。

### <a name="helmcommanderror"></a>HELM_COMMAND_ERROR

此错误意味着通过 CLI，它可以阻止其他 helm 操作找不到该 helm 客户端。

*可能的解决方案*： 验证该 helm 安装客户端，并且其路径添加到系统环境变量。

### <a name="helmversionerror"></a>HELM_VERSION_ERROR

此错误意味着 CLI 不能确定安装的 Helm 版本。 如果发生这种情况的 Azure CLI 版本 (或者，如果 helm 版本) 正在使用已过时。

*可能的解决方案*： 更新到最新的 Azure CLI 版本或建议的 helm 版本; 手动运行命令，并调查的错误消息。

### <a name="connectivitydnserror"></a>CONNECTIVITY_DNS_ERROR

此错误表示给定的注册表登录服务器的 DNS 其执行 ping 操作，但未响应，这意味着它是不可用。 这可以表示某些连接问题。 这还意味着，在注册表或不存在，用户在注册表中 （若要检索其登录服务器正确），没有权限，目标注册表中之外的另一个云中正在使用 Azure CLI。

*可能的解决方案*： 验证连接; 验证注册表中，拼写和该注册表存在; 验证用户在其上具有适当的权限以及注册表的云是相同所使用的 Azure CLI。

### <a name="connectivityforbiddenerror"></a>CONNECTIVITY_FORBIDDEN_ERROR

这意味着在给定注册表的质询终结点响应 403 禁止访问 HTTP 状态。 这意味着，用户无权访问注册表中，很可能是因为 VNET 配置。

*可能的解决方案*： 删除 VNET 规则或将当前的客户端 IP 添加到允许列表。

### <a name="connectivitychallengeerror"></a>CONNECTIVITY_CHALLENGE_ERROR

此错误表示目标注册表的质询终结点没有颁发一个挑战。

*可能的解决方案*： 段时间后重试。 如果错误仍然存在，请打开在上午问题 https://aka.ms/acr/issues 。

### <a name="connectivityaadloginerror"></a>CONNECTIVITY_AAD_LOGIN_ERROR

此错误表示颁发目标注册表的质询终结点的一种挑战，但注册表不支持 AAD 登录名。

*可能的解决方案*： 尝试在中，例如，管理员凭据的日志记录的另一种方法。 如果用户需要能够使用 AAD 支持进行登录，请打开在上午问题 https://aka.ms/acr/issues 。

### <a name="connectivityrefreshtokenerror"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

这意味着注册表登录服务器未响应使用刷新令牌，这意味着对目标注册表的访问权限被拒绝。 如果用户对注册表没有适当的权限或 Azure CLI 的用户凭据已过时，则可以发生这种情况。

*可能的解决方案*： 验证用户是否对注册表有适当的权限; 运行`az login`刷新权限、 令牌和凭据。

### <a name="connectivityaccesstokenerror"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

这意味着注册表登录服务器未响应与访问令牌，这意味着对目标注册表的访问权限被拒绝。 如果用户对注册表没有适当的权限或 Azure CLI 的用户凭据已过时，则可以发生这种情况。

*可能的解决方案*： 验证用户是否对注册表有适当的权限; 运行`az login`刷新权限、 令牌和凭据。

### <a name="loginservererror"></a>LOGIN_SERVER_ERROR

这意味着，CLI 找不到给定注册表登录服务器，没有默认后缀未找到当前的云。 这可能是如果注册表不存在，如果用户没有适当的权限在注册表中，如果注册表的云和当前的 Azure CLI 云不匹配，或 Azure CLI 版本已过时。

*可能的解决方案*： 验证的拼写是否正确和注册表存在; 如果验证用户是否具有适当的权限在注册表中，并且，与匹配的注册表和 CLI 环境的云; 更新到最新版本的 Azure CLI。

## <a name="next-steps"></a>后续步骤

* [了解详细信息](container-registry-intro.md)了解 Azure 容器注册表。