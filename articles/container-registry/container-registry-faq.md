---
title: 常见问题
description: 有关 Azure 容器注册表服务的常见问题的解答
author: sajayantony
ms.topic: article
ms.date: 03/18/2020
ms.author: sajaya
ms.openlocfilehash: 39b543c5f886b22d488198873b75cf76555692fa
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82731638"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>有关 Azure 容器注册表的常见问题解答

本文解答有关 Azure 容器注册表的常见问题和已知问题。

## <a name="resource-management"></a>资源管理

- [是否可以使用资源管理器模板创建 Azure 容器注册表？](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [是否会对 ACR 中的映像执行安全漏洞扫描？](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [如何使用 Azure 容器注册表配置 Kubernetes？](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [如何获取容器注册表的管理员凭据？](#how-do-i-get-admin-credentials-for-a-container-registry)
- [如何获取资源管理器模板中的管理员凭据？](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [尽管使用 Azure CLI 或 Azure PowerShell 删除了复制，但删除复制操作仍然失败并出现“已禁止”状态](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [防火墙规则已成功更新，但不生效](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>是否可以使用资源管理器模板创建 Azure 容器注册表？

是的。 下面是可用于创建注册表的[模版](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry)。

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>是否会对 ACR 中的映像执行安全漏洞扫描？

是的。 请参阅[Azure 安全中心](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration)、 [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/)和[浅绿](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry)中的文档。

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>如何使用 Azure 容器注册表配置 Kubernetes？

请参阅 [Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) 的文档以及适用于 [Azure Kubernetes 服务](../aks/cluster-container-registry-integration.md)的步骤。

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>如何获取容器注册表的管理员凭据？

> [!IMPORTANT]
> 管理员用户帐户专门用于单个用户访问注册表，主要用于测试目的。 建议不要与多个用户共享管理员帐户凭据。 建议用户和服务主体在无外设方案中使用单个标识。 请参阅[身份验证概述](container-registry-authentication.md)。

在获取管理员凭据之前，请确保已启用注册表的管理员用户。

使用 Azure CLI 获取凭据：

```azurecli
az acr credential show -n myRegistry
```

使用 Azure PowerShell：

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>如何获取资源管理器模板中的管理员凭据？

> [!IMPORTANT]
> 管理员用户帐户专门用于单个用户访问注册表，主要用于测试目的。 建议不要与多个用户共享管理员帐户凭据。 建议用户和服务主体在无外设方案中使用单个标识。 请参阅[身份验证概述](container-registry-authentication.md)。

在获取管理员凭据之前，请确保已启用注册表的管理员用户。

获取第一个密码：

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

获取第二个密码：

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>尽管使用 Azure CLI 或 Azure PowerShell 删除了复制，但删除复制操作仍然失败并出现“已禁止”状态

如果用户对某个注册表拥有权限，但对订阅没有读取者级别的权限，则会出现此错误。 若要解决此问题，请向用户分配对订阅的“读取者”权限：


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>防火墙规则已成功更新，但不生效

传播防火墙规则更改需要一段时间。 更改防火墙设置后，请等待几分钟，然后验证此更改。


## <a name="registry-operations"></a>注册表操作

- [如何访问 Docker 注册表 HTTP API V2？](#how-do-i-access-docker-registry-http-api-v2)
- [如何删除不由存储库中的任何标记引用的所有清单？](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [为何删除映像后，注册表配额用量未减少？](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [如何验证存储配额更改？](#how-do-i-validate-storage-quota-changes)
- [在容器中运行 CLI 时如何对注册表进行身份验证？](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [如何启用 TLS 1.2？](#how-to-enable-tls-12)
- [Azure 容器注册表是否支持内容信任？](#does-azure-container-registry-support-content-trust)
- [在无权管理注册表资源的情况下如何授予提取或推送映像的访问权限？](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [如何实现为注册表启用自动映像隔离？](#how-do-i-enable-automatic-image-quarantine-for-a-registry)
- [如何实现启用匿名请求访问？](#how-do-i-enable-anonymous-pull-access)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>如何访问 Docker 注册表 HTTP API V2？

ACR 支持 Docker 注册表 HTTP API V2。 可通过 `https://<your registry login server>/v2/` 访问 API。 示例： `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>如何删除不由存储库中的任何标记引用的所有清单？

在 bash 中：

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

在 PowerShell 中：

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

注意：在 delete 命令中添加 `-y` 可跳过确认。

有关详细信息，请参阅[删除 Azure 容器注册表中的容器映像](container-registry-delete.md)。

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>为何删除映像后，注册表配额用量未减少？

如果其他容器映像仍在引用基础层，则可能会发生这种情况。 如果删除不带引用的映像，则注册表用量在几分钟后即会更新。

### <a name="how-do-i-validate-storage-quota-changes"></a>如何验证存储配额更改？

使用以下 Docker 文件创建使用 1GB 层的映像。 这可以确保该映像的某个层不会由注册表中的任何其他映像共享。

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

使用 Docker CLI 生成映像并将其推送到注册表。

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

在 Azure 门户中应该可以看到存储用量已增加，或者可以使用 CLI 查询用量。

```azurecli
az acr show-usage -n myregistry
```

使用 Azure CLI 或门户删除映像，并在几分钟后检查更新的用量。

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>在容器中运行 CLI 时如何对注册表进行身份验证？

需要通过装载 Docker 套接字来运行 Azure CLI 容器：

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

在容器中安装 `docker`：

```bash
apk --update add docker
```

然后对注册表进行身份验证：

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>如何启用 TLS 1.2？

使用任何最近发布的 Docker 客户端（18.03.0 和更高版本）启用 TLS 1.2。 

> [!IMPORTANT]
> 从 2020 年 1 月 13 日开始，Azure 容器注册表将要求服务器和应用程序的所有安全连接都使用 TLS 1.2。 对 TLS 1.0 和 1.1 的支持将停用。

### <a name="does-azure-container-registry-support-content-trust"></a>Azure 容器注册表是否支持内容信任？

支持，可以在 Azure 容器注册表中使用受信任映像，因为 [Docker Notary](https://docs.docker.com/notary/getting_started/) 已集成且可以启用。 有关详细信息，请参阅 [Azure 容器注册表中的内容信任](container-registry-content-trust.md)。


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>指纹文件位于何处？

在 `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata` 下：

* 所有角色（委托角色除外）的公钥和证书都存储在 `root.json` 中。
* 委托角色的公钥和证书将存储在其父角色的 JSON 文件（例如，`targets/releases` 角色的 `targets.json`）中。

建议在 Docker 和 Notary 客户端完成总体 TUF 验证后验证这些公钥和证书。

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>在无权管理注册表资源的情况下如何授予提取或推送映像的访问权限？

ACR 支持提供不同权限级别的[自定义角色](container-registry-roles.md)。 具体而言，`AcrPull` 和 `AcrPush` 角色允许用户在无权管理 Azure 中的注册表资源的情况下提取和/或推送映像。

* Azure 门户：你的注册表 >“访问控制(IAM)”->“添加”（为“角色”选择 `AcrPull` 或 `AcrPush`）。
* Azure CLI：运行以下命令找到注册表的资源 ID：

  ```azurecli
  az acr show -n myRegistry
  ```
  
  然后，可将 `AcrPull` 或 `AcrPush` 角色分配给用户（以下示例使用 `AcrPull`）：

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  或者，将角色分配到由应用程序 ID 标识的服务主体：

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

然后，被分配者可以验证和访问注册表中的映像。

* 对注册表进行身份验证：
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* 列出存储库：

  ```azurecli
  az acr repository list -n myRegistry
  ```

* 提取映像：

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

如果只使用 `AcrPull` 或 `AcrPush` 角色，则被分配者将无权管理 Azure 中的注册表资源。 例如，`az acr list` 或 `az acr show -n myRegistry` 不会显示注册表。

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>如何为注册表启用自动映像隔离？

映像隔离目前是 ACR 的预览版功能。 可以启用注册表的隔离模式，使普通用户只能看到已成功通过安全扫描的映像。 有关详细信息，请参阅 [ACR GitHub 存储库](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)。

### <a name="how-do-i-enable-anonymous-pull-access"></a>如何实现启用匿名请求访问？

为匿名（公共）拉取访问设置 Azure 容器注册表目前是一项预览功能。 若要启用公共访问，请在处https://aka.ms/acr/support/create-ticket打开支持票证。 有关详细信息，请参阅[Azure 反馈论坛](https://feedback.azure.com/forums/903958-azure-container-registry/suggestions/32517127-enable-anonymous-access-to-registries)。


## <a name="diagnostics-and-health-checks"></a>诊断和运行状况检查

- [使用 `az acr check-health` 检查运行状况](#check-health-with-az-acr-check-health)
- [Docker 提取失败并出现错误：net/http: 等待连接时取消了请求(等待标头时超过了 Client.Timeout)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [Docker 推送成功，但 Docker 提取失败并出现错误：未授权: 需要身份验证](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login` 成功，但 Docker 命令失败并出现错误：未授权: 需要身份验证](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [启用和获取 Docker 守护程序的调试日志](#enable-and-get-the-debug-logs-of-the-docker-daemon)    
- [新用户权限在更新后可能不会立即生效](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [未在 REST API 调用中以正确的格式指定身份验证信息](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [为何 Azure 门户不列出我的所有存储库或标记？](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [为何 Azure 门户无法提取存储库或标记？](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [为什么我的拉取或推送请求失败，出现操作不被允许的情况？](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [如何在 Windows 上收集 HTTP 跟踪？](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>使用 `az acr check-health` 检查运行状况

若要排查常见的环境和注册表问题，请参阅[检查 Azure 容器注册表的运行状况](container-registry-check-health.md)。

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>Docker 提取失败并出现错误：net/http: 等待连接时取消了请求(等待标头时超过了 Client.Timeout)

 - 如果此错误是暂时性的，则重试后，提取将会成功。
 - 如果 `docker pull` 连续失败，则原因可能是 Docker 守护程序出现了问题。 重启 Docker 守护程序通常可以缓解此问题。 
 - 如果重启 Docker 守护程序后仍旧出现此问题，则原因可能是计算机上出现了一些网络连接问题。 若要检查计算机上的常规网络是否正常，请运行以下命令来测试终结点连接。 包含此连接性检查命令的最低 `az acr` 版本为 2.2.9。 如果使用早期的版本，请升级 Azure CLI。
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - 始终应该对所有 Docker 客户端操作使用重试机制。

### <a name="docker-pull-is-slow"></a>Docker 提取速度缓慢
使用[此](http://www.azurespeed.com/Azure/Download)工具测试计算机的网络下载速度。 如果计算机网络速度较慢，请考虑在注册表所在的同一区域中使用 Azure VM。 这通常可以加快网络速度。

### <a name="docker-push-is-slow"></a>Docker 推送速度缓慢
使用[此](http://www.azurespeed.com/Azure/Upload)工具测试计算机的网络上传速度。 如果计算机网络速度较慢，请考虑在注册表所在的同一区域中使用 Azure VM。 这通常可以加快网络速度。

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Docker 推送成功，但 Docker 提取失败并出现错误：未授权: 需要身份验证

默认启用 `--signature-verification` 的 Red Hat 版 Docker 守护程序可能会发生此错误。 可以运行以下命令来检查 Red Hat Enterprise Linux (RHEL) 或 Fedora 的 Docker 守护程序选项：

```bash
grep OPTIONS /etc/sysconfig/docker
```

例如，Fedora 28 服务器使用以下 Docker 守护程序选项：

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

如果缺少 `--signature-verification=false`，`docker pull` 将会失败并出现如下所示的错误：

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

若要解决该错误：
1. 将选项 `--signature-verification=false` 添加到 Docker 守护程序配置文件 `/etc/sysconfig/docker`。 例如：
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. 运行以下命令重启 Docker 守护程序服务：
   
   ```bash
   sudo systemctl restart docker.service
   ```

运行 `man dockerd` 可以找到 `--signature-verification` 的详细信息。

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>az acr login 成功，但 Docker 失败并出现错误：未授权: 需要身份验证

请确保使用全小写的服务器 URL（例如 `docker push myregistry.azurecr.io/myimage:latest`），即使注册表资源名称是大写的或大小写混合的（例如 `myRegistry`）。

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>启用和获取 Docker 守护程序的调试日志    

使用 `debug` 选项启动 `dockerd`。 首先创建 Docker 守护程序配置文件 (`/etc/docker/daemon.json`)（如果不存在），并添加 `debug` 选项：

```json
{    
    "debug": true    
}
```

然后重启守护程序。 例如，对于 Ubuntu 14.04：

```bash
sudo service docker restart
```

可以在 [Docker 文档](https://docs.docker.com/engine/admin/#enable-debugging)中找到详细信息。    

 * 日志可能在不同的位置生成，具体取决于所用的系统。 例如，对于 Ubuntu 14.04，日志位置为 `/var/log/upstart/docker.log`。    
有关详细信息，请参阅 [Docker 文档](https://docs.docker.com/engine/admin/#read-the-logs)。    

 * 对于用于 Windows 的 Docker，将在 %LOCALAPPDATA%/docker/ 下生成日志。 但是，此位置不一定包含所有调试信息。    

   若要访问完整的守护程序日志，可能需要执行一些额外的步骤：

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    现在，可以访问运行 `dockerd` 的 VM 的所有文件。 日志位于 `/var/log/docker.log`。

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>新用户权限在更新后可能不会立即生效

向服务主体授予新权限（新角色）时，更改可能不会立即生效。 有两种可能的原因：

* Azure Active Directory 角色分配延迟。 通常分配速度很快，但由于传播延迟，此过程可能需要几分钟时间。
* ACR 令牌服务器上出现权限延迟。 分配权限最长可能需要 10 分钟。 若要缓解此情况，可以运行 `docker logout`，并在 1 分钟后使用同一用户的身份再次进行身份验证：

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

目前，ACR 不支持用户删除宿主复制。 解决方法是在模板中包括宿主复制创建指令，但通过添加如下所示的 `"condition": false` 来跳过宿主复制的创建：

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

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>未在 REST API 调用中以正确的格式指定身份验证信息

你可能会遇到 `InvalidAuthenticationInfo` 错误，尤其是结合选项 `-L`（`--location`，跟踪重定向）使用 `curl` 工具时。
例如，结合 `-L` 选项和基本身份验证使用 `curl` 提取 Blob：

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

可能会生成以下响应：

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

根本原因是某些 `curl` 实现使用原始请求中的标头跟踪重定向。

若要解决此问题，需要在不使用标头的情况下手动跟踪重定向。 使用 `curl` 的 `-D -` 选项输出响应标头，然后提取 `Location` 标头：

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>为何 Azure 门户不列出我的所有存储库或标记？ 

如果使用 Microsoft Edge/IE 浏览器，则最多可以查看 100 个存储库或标记。 如果注册表中的存储库或标记超过 100 个，则我们建议使用 Firefox 或 Chrome 浏览器将其列出。

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>为何 Azure 门户无法提取存储库或标记？

浏览器可能无法向服务器发送请求来提取存储库或标记。 可能有各种原因，例如：

* 缺少网络连接
* 防火墙
* 广告阻止程序
* DNS 错误

请与网络管理员联系，或者检查网络配置和连接性。 尝试使用 Azure CLI 来运行 `az acr check-health -n yourRegistry`，以便检查环境是否能够连接到容器注册表。 另外，也可尝试在浏览器中使用 incognito 或专用会话，避免使用任何过时的浏览器缓存或 Cookie。

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>为什么我的拉取或推送请求失败，出现操作不被允许的情况？

下面是操作可能不被允许的一些情况：
* 不再支持经典注册表。 请使用 [az acr update](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-update) 或 Azure 门户升级到受支持的 [SKU](https://aka.ms/acr/skus)。
* 映像或存储库可能会处于锁定状态，导致无法删除或更新。 可以使用 [az acr show repository](https://docs.microsoft.com/azure/container-registry/container-registry-image-lock) 命令来查看当前属性。
* 如果映像处于隔离状态，则会禁用某些操作。 详细了解[隔离](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)。

### <a name="how-do-i-collect-http-traces-on-windows"></a>如何在 Windows 上收集 HTTP 跟踪？

#### <a name="prerequisites"></a>先决条件

- 在 Fiddler 中启用 HTTPS 解密：<https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- 允许 Docker 通过 Docker UI 使用代理：<https://docs.docker.com/docker-for-windows/#proxies>
- 完成后，请务必还原设置。  在启用此设置的情况下，Docker 不会正常工作，并且 Fiddler 不会运行。

#### <a name="windows-containers"></a>Windows 容器

将 Docker 代理配置为 127.0.0.1:8888

#### <a name="linux-containers"></a>Linux 容器

查找 Docker VM 虚拟交换机的 IP：

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

将 Docker 代理配置为上一命令的输出和端口 8888（例如 10.0.75.1:8888）

## <a name="tasks"></a>任务

- [如何批量取消运行？](#how-do-i-batch-cancel-runs)
- [如何在 az acr build 命令中包含 .git 文件夹？](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [任务是否支持用于源触发器的 GitLab？](#does-tasks-support-gitlab-for-source-triggers)
- [任务支持什么 git 存储库管理服务？](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>如何批量取消运行？

以下命令可取消指定的注册表中所有正在运行的任务。

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>如何在 az acr build 命令中包含 .git 文件夹？

如果将本地源文件夹传递到 `az acr build` 命令，则默认会从上传的包中排除 `.git` 文件夹。 可以使用以下设置创建 `.dockerignore` 文件。 这会告知命令还原已上传包中 `.git` 下的所有文件。 

`!.git/**`

此设置也适用于 `az acr run` 命令。

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>任务是否支持用于源触发器的 GitLab？

我们目前不支持用于源触发器的 GitLab。

### <a name="what-git-repository-management-service-does-tasks-support"></a>任务支持什么 git 存储库管理服务？

| Git 服务 | 源上下文 | 手动生成 | 通过“提交”触发器自动生成 |
|---|---|---|---|
| GitHub | `https://github.com/user/myapp-repo.git#mybranch:myfolder` | 是 | 是 |
| Azure Repos | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` | 是 | 是 |
| GitLab | `https://gitlab.com/user/myapp-repo.git#mybranch:myfolder` | 是 | 否 |
| BitBucket | `https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder` | 是 | 否 |

## <a name="run-error-message-troubleshooting"></a>运行错误消息故障排除操作

| 错误消息 | 故障排除指南 |
|---|---|
|未为 VM 配置任何访问权限，因此未找到订阅|如果在 ACR 任务中使用 `az login --identity`，则可能发生这种情况。 这是暂时性错误，在托管标识的角色分配无法传播时发生。 请等几秒钟，然后重试即可。|

## <a name="cicd-integration"></a>CI/CD 集成

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub 操作](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>后续步骤

* [详细了解](container-registry-intro.md) Azure 容器注册表。
