---
title: 常见问题
description: 有关 Azure 容器注册表服务的常见问题解答
author: sajayantony
ms.topic: article
ms.date: 03/18/2020
ms.author: sajaya
ms.openlocfilehash: 005c035468a4225f96e8ef69b2ef31a82bf7eedb
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682826"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>有关 Azure 容器注册表的常见问题解答

本文解答有关 Azure 容器注册表的常见问题和已知问题。

## <a name="resource-management"></a>资源管理

- [是否可以使用资源管理器模板创建 Azure 容器注册表？](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [ACR 中是否提供对映像的安全漏洞扫描？](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [如何使用 Azure 容器注册表配置 Kubernetes？](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [如何获取容器注册表的管理员凭据？](#how-do-i-get-admin-credentials-for-a-container-registry)
- [如何在资源管理器模板中获取管理员凭据？](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [尽管使用 Azure CLI 或 Azure PowerShell 删除了复制，但复制删除失败且处于禁止状态](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [防火墙规则已成功更新，但未生效](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>是否可以使用资源管理器模板创建 Azure 容器注册表？

是的。 这里提供了[模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry)，可以用来创建注册表。

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>ACR 中是否提供对映像的安全漏洞扫描？

是的。 请参阅 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration)、[Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) 和 [Aqua](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry) 中的文档。

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>如何使用 Azure 容器注册表配置 Kubernetes？

请参阅 [Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) 的文档和 [Azure Kubernetes 服务](../aks/cluster-container-registry-integration.md)的步骤。

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

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>如何在资源管理器模板中获取管理员凭据？

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

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>尽管使用 Azure CLI 或 Azure PowerShell 删除了复制，但复制删除失败且处于禁止状态

当用户对注册表具有权限，但对订阅没有读者级别的权限时，便会看到此错误。 若要解决此问题，请将订阅的读者权限分配给用户：


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>防火墙规则已成功更新，但未生效

传播防火墙规则更改需要一些时间。 更改防火墙设置后，请等待几分钟，然后再验证此更改。


## <a name="registry-operations"></a>注册表操作

- [如何访问 Docker 注册表 HTTP API V2？](#how-do-i-access-docker-registry-http-api-v2)
- [如何删除存储库中任何标记均未引用的所有清单？](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [为什么删除映像后注册表配额使用率没有降低？](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [如何验证存储配额更改？](#how-do-i-validate-storage-quota-changes)
- [在容器中运行 CLI 时，如何使用注册表进行身份验证？](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [如何启用 TLS 1.2？](#how-to-enable-tls-12)
- [Azure 容器注册表是否支持内容信任？](#does-azure-container-registry-support-content-trust)
- [如何在无权管理注册表资源的情况下授予“拉取”或“推送”映像的访问权限？](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [如何为注册表启用自动映像隔离？](#how-do-i-enable-automatic-image-quarantine-for-a-registry)
- [如何启用匿名拉取访问？](#how-do-i-enable-anonymous-pull-access)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>如何访问 Docker 注册表 HTTP API V2？

ACR 支持 Docker 注册表 HTTP API V2。 可以通过 `https://<your registry login server>/v2/` 访问 API。 示例： `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>如何删除存储库中任何标记均未引用的所有清单？

如果你使用的是 bash：

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

对于 PowerShell：

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

注意：可以在删除命令中添加 `-y` 以跳过确认。

有关详细信息，请参阅[删除 Azure 容器注册表中的容器映像](container-registry-delete.md)。

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>为什么删除映像后注册表配额使用率没有降低？

如果基础层仍被其他容器映像引用，则可能发生这种情况。 如果删除没有引用的映像，注册表使用情况将在几分钟内更新。

### <a name="how-do-i-validate-storage-quota-changes"></a>如何验证存储配额更改？

使用以下 docker 文件创建一个包含 1GB 层的映像。 这可以确保该映像有一个未被注册表中的任何其他映像共享的层。

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

使用 docker CLI 生成映像并将其推送到注册表。

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

你应该能够看到 Azure 门户中的存储使用量有所增加，或者你可以使用 CLI 查询使用量。

```azurecli
az acr show-usage -n myregistry
```

使用 Azure CLI 或门户删除映像，并在几分钟后检查更新后的使用量。

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>在容器中运行 CLI 时，如何使用注册表进行身份验证？

需要通过安装 Docker 套接字来运行 Azure CLI 容器：

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

在容器中，安装 `docker`：

```bash
apk --update add docker
```

然后，使用注册表进行身份验证：

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>如何启用 TLS 1.2？

使用任何最近的 Docker 客户端（版本 18.03.0 及更高版本）启用 TLS 1.2。 

> [!IMPORTANT]
> 从 2020 年 1 月 13 日开始，Azure 容器注册表将要求服务器和应用程序的所有安全连接都使用 TLS 1.2。 对 TLS 1.0 和 1.1 的支持将停用。

### <a name="does-azure-container-registry-support-content-trust"></a>Azure 容器注册表是否支持内容信任？

是的，你可以在 Azure 容器注册表中使用受信任的映像，因为 [Docker Notary](https://docs.docker.com/notary/getting_started/) 已经集成并且可以启用。 有关详细信息，请参阅 [Azure 容器注册表中的内容信任](container-registry-content-trust.md)。


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>指纹文件位于何处？

在 `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata` 下：

* 所有角色（委派角色除外）的公钥和证书都存储在 `root.json` 中。
* 委派角色的公钥和证书存储在其父级角色的 JSON 文件中（例如，`targets/releases` 角色的 `targets.json`）。

建议在 Docker 和 Notary 客户端完成整体 TUF 验证后再对这些公钥和证书进行验证。

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>如何在无权管理注册表资源的情况下授予“拉取”或“推送”映像的访问权限？

ACR 支持提供不同权限级别的[自定义角色](container-registry-roles.md)。 具体来说，`AcrPull` 和 `AcrPush` 角色允许用户在无权管理 Azure 中注册表资源的情况下拉取和/或推送映像。

* Azure 门户：“注册表”->“访问控制(IAM)”->“添加”（对于“角色”，请选择 `AcrPull` 或 `AcrPush`）。
* Azure CLI：通过运行以下命令查找注册表的资源 ID：

  ```azurecli
  az acr show -n myRegistry
  ```
  
  然后，可以将 `AcrPull` 或 `AcrPush` 角色分配给用户（以下示例使用 `AcrPull`）：

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  或者，将角色分配给由其应用程序 ID 标识的服务主体：

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

然后，代理人可以在注册表中验证和访问映像。

* 对注册表进行身份验证：
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* 列出存储库：

  ```azurecli
  az acr repository list -n myRegistry
  ```

* 拉取映像：

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

仅使用 `AcrPull` 或 `AcrPush` 角色时，代理人无权管理 Azure 中的注册表资源。 例如，`az acr list` 或 `az acr show -n myRegistry` 不会显示注册表。

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>如何为注册表启用自动映像隔离？

映像隔离目前是 ACR 的一项预览功能。 你可以启用注册表的隔离模式，以便只有那些已成功通过安全扫描的映像对普通用户可见。 有关详细信息，请参阅 [ACR GitHub 存储库](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)。

### <a name="how-do-i-enable-anonymous-pull-access"></a>如何启用匿名拉取访问？

为匿名（公共）拉取访问设置 Azure 容器注册表目前是一项预览功能。 若要启用公共访问，请在 https://aka.ms/acr/support/create-ticket 开具支持票证。 有关详细信息，请参阅 [Azure 反馈论坛](https://feedback.azure.com/forums/903958-azure-container-registry/suggestions/32517127-enable-anonymous-access-to-registries)。


## <a name="diagnostics-and-health-checks"></a>诊断和运行状况检查

- [使用 `az acr check-health` 检查运行状况](#check-health-with-az-acr-check-health)
- [docker pull 失败，出现错误：net/http：等待连接时请求被取消（等待标头时 Client.Timeout 超时）](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [docker 推送成功但 docker pull 失败，出现错误：未授权：需要进行身份验证](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login` 成功但 docker 命令失败，出现错误：未授权：需要进行身份验证](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [启用并获取 docker 守护程序的调试日志](#enable-and-get-the-debug-logs-of-the-docker-daemon)    
- [更新后，新用户权限可能不会立即生效](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [直接 REST API 调用的身份验证信息的格式不正确](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [为什么 Azure 门户未列出我的所有存储库或标记？](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [为什么 Azure 门户无法获取存储库或标记？](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [为什么拉取或推送请求失败，并出现不受允许的操作？](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [如何在 Windows 上收集 http 跟踪？](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>使用 `az acr check-health` 检查运行状况

若要排查常见的环境和注册表问题，请参阅[检查 Azure 容器注册表的运行状况](container-registry-check-health.md)。

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>docker pull 失败，出现错误：net/http：等待连接时请求被取消（等待标头时 Client.Timeout 超时）

 - 如果此错误是暂时性问题，则重试将成功。
 - 如果 `docker pull` 连续失败，则 Docker 守护程序可能出现问题。 通常可以通过重新启动 Docker 守护程序来缓解此问题。 
 - 如果重新启动 Docker 守护程序后继续出现此问题，则问题可能是与计算机相关的某些网络连接问题。 若要检查计算机上的常规网络是否正常，请运行以下命令测试终结点连接。 包含此连接性检查命令的 `az acr` 最低版本是 2.2.9。 如果使用的是较旧版本，请升级 Azure CLI。
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - 所有 Docker 客户端操作都应该具有重试机制。

### <a name="docker-pull-is-slow"></a>Docker pull 速度缓慢
使用[此](http://www.azurespeed.com/Azure/Download)工具来测试计算机网络下载速度。 如果计算机网络速度较慢，请考虑使用与注册表位于同一区域的 Azure VM。 这通常能够为你提供更快的网络速度。

### <a name="docker-push-is-slow"></a>Docker 推送速度缓慢
使用[此](http://www.azurespeed.com/Azure/Upload)工具测试计算机网络上载速度。 如果计算机网络速度较慢，请考虑使用与注册表位于同一区域的 Azure VM。 这通常能够为你提供更快的网络速度。

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Docker 推送成功但 docker pull 失败，出现错误：未授权：需要进行身份验证

此错误可能发生在 Docker 守护程序的 Red Hat 版本上，默认情况下，`--signature-verification` 处于启用状态。 通过运行以下命令，可以检查 Red Hat Enterprise Linux (RHEL) 或 Fedora 的 Docker 守护程序选项：

```bash
grep OPTIONS /etc/sysconfig/docker
```

例如，Fedora 28 服务器具有以下 docker 守护程序选项：

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

缺少 `--signature-verification=false` 时，`docker pull` 失败，并出现类似以下的错误：

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

解决该错误：
1. 将选项 `--signature-verification=false` 添加到 Docker 守护程序配置文件 `/etc/sysconfig/docker`。 例如：
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. 通过运行以下命令重新启动 Docker 守护程序服务：
   
   ```bash
   sudo systemctl restart docker.service
   ```

运行 `man dockerd` 可以找到 `--signature-verification` 的详细信息。

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>az acr 登录成功，但 docker 失败并出现错误：未授权：需要进行身份验证

确保使用全小写的服务器 URL，例如 `docker push myregistry.azurecr.io/myimage:latest`，即使注册表资源名称是大写或混合大小写，如 `myRegistry`。

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>启用并获取 docker 守护程序的调试日志    

使用 `debug` 选项启动 `dockerd`。 首先，如果 Docker 守护程序配置文件不存在，则创建该文件 (`/etc/docker/daemon.json`)，并添加 `debug` 选项：

```json
{    
    "debug": true    
}
```

然后重新启动该守护程序。 例如，对于 Ubuntu 14.04：

```bash
sudo service docker restart
```

可以在 [Docker 文档](https://docs.docker.com/engine/admin/#enable-debugging)中找到详细信息。    

 * 日志可能在不同的位置生成，具体取决于你的系统。 例如，对于 Ubuntu 14.04，日志位于 `/var/log/upstart/docker.log` 中。    
有关详细信息，请参阅[Docker 文档](https://docs.docker.com/engine/admin/#read-the-logs)。    

 * 对于用于 Windows 的 Docker，将在 %LOCALAPPDATA%/docker/ 下生成日志。 但是它可能未包含所有的调试信息。    

   若要访问完整的守护程序日志，可能需要执行一些额外的步骤：

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    现在，你可以访问运行 `dockerd` 的 VM 的所有文件。 日志位于 `/var/log/docker.log`。

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>更新后，新用户权限可能不会立即生效

向服务主体授予新权限（新角色）时，更改可能不会立即生效。 有两个可能的原因：

* Azure Active Directory 角色分配延迟。 通常这种分配的速度很快，但由于传播延迟，可能需要几分钟的时间。
* ACR 令牌服务器上的权限延迟。 这可能最多需要10分钟时间。 若要缓解这种情况，你可以 `docker logout`，然后在 1 分钟后再次对同一用户进行身份验证：

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

目前，ACR 不支持用户删除主复制。 规避方法是在模板中包含主复制创建，但通过添加 `"condition": false` 跳过其创建，如下所示：

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

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>直接 REST API 调用的身份验证信息的格式不正确

你可能会遇到 `InvalidAuthenticationInfo` 错误，尤其是使用带有 `-L`、`--location` 选项（以遵循重定向）的 `curl` 工具时。
例如，使用带有 `-L` 选项的 `curl` 和基本身份验证来提取 blob：

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

根本原因是某些 `curl` 实现使用来自原始请求的标头遵循重定向。

若要解决此问题，需要在没有标头的情况下手动遵循重定向。 使用 `curl` 的 `-D -` 选项打印响应标头，然后提取 `Location` 标头：

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>为什么 Azure 门户未列出我的所有存储库或标记？ 

如果使用的是 Microsoft Edge/IE 浏览器，则最多可以查看 100 个存储库或标记。 如果你的注册表中有超过 100 个存储库或标记，我们建议你使用 Firefox 或 Chrome 浏览器以将它们全部列出。

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>为什么 Azure 门户无法获取存储库或标记？

浏览器可能无法向服务器发送提取存储库或标记的请求。 可能有多种原因，例如：

* 缺少网络连接
* 防火墙
* 广告阻止程序
* DNS 错误

请与网络管理员联系或检查网络配置和连接。 尝试使用 Azure CLI 运行 `az acr check-health -n yourRegistry` 以检查环境是否能够连接到容器注册表。 此外，还可以尝试在浏览器中使用隐身模式或专用会话，以避免任何过时的浏览器缓存或 Cookie。

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>为什么拉取或推送请求失败，并出现不受允许的操作？

以下是一些可能出现不允许进行操作的情况：
* 不再支持经典注册表。 请使用 [az acr update](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-update)或 Azure 门户升级到受支持的[服务层](https://aka.ms/acr/skus)。
* 映像或存储库可能已锁定，因此无法进行删除或更新。 可以使用 [az acr show repository](https://docs.microsoft.com/azure/container-registry/container-registry-image-lock) 命令查看当前属性。
* 如果映像处于隔离状态，则不允许进行某些操作。 详细了解[隔离](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)。

### <a name="how-do-i-collect-http-traces-on-windows"></a>如何在 Windows 上收集 http 跟踪？

#### <a name="prerequisites"></a>先决条件

- 在 fiddler 中启用解密 https：<https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- 通过 Docker UI 使 Docker 使用代理：<https://docs.docker.com/docker-for-windows/#proxies>
- 完成后，请务必还原。  Docker 无法在启用此项且 Fiddler 未运行的情况下工作。

#### <a name="windows-containers"></a>Windows 容器

将 Docker 代理配置为 127.0.0.1:8888

#### <a name="linux-containers"></a>Linux 容器

查找 Docker VM 虚拟交换机的 IP：

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

将 Docker 代理配置为上一个命令和端口 8888 的输出（例如 10.0.75.1:8888）

## <a name="tasks"></a>任务

- [如何批量取消运行？](#how-do-i-batch-cancel-runs)
- [如何在 az acr 生成命令中包含 .git 文件夹？](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [任务是否支持 GitLab for Source 触发器？](#does-tasks-support-gitlab-for-source-triggers)
- [任务支持哪些 git 存储库管理服务？](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>如何批量取消运行？

下列命令取消指定注册表中所有正在运行的任务。

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>如何在 az acr 生成命令中包含 .git 文件夹？

如果将本地源文件夹传递给 `az acr build` 命令，则默认情况下，`.git` 文件夹将从已上传的包中排除。 可以使用以下设置创建 `.dockerignore` 文件。 它会通知命令还原上传的包中 `.git` 下的所有文件。 

`!.git/**`

此设置还适用于 `az acr run` 命令。

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>任务是否支持 GitLab for Source 触发器？

我们目前不支持 GitLab for Source 触发器。

### <a name="what-git-repository-management-service-does-tasks-support"></a>任务支持哪些 git 存储库管理服务？

| Git 服务 | 源上下文 | 手动生成 | 通过提交触发器自动生成 |
|---|---|---|---|
| GitHub | `https://github.com/user/myapp-repo.git#mybranch:myfolder` | 是 | 是 |
| Azure Repos | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` | 是 | 是 |
| GitLab | `https://gitlab.com/user/myapp-repo.git#mybranch:myfolder` | 是 | 否 |
| BitBucket | `https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder` | 是 | 否 |

## <a name="run-error-message-troubleshooting"></a>运行错误消息故障排除

| 错误消息 | 故障排除指南 |
|---|---|
|未为 VM 配置访问权限，因此找不到订阅|如果在 ACR 任务中使用 `az login --identity`，则可能发生这种情况。 这是暂时性的错误，会在未传播托管标识的角色分配时发生。 请等待几秒钟，然后重试。|

## <a name="cicd-integration"></a>CI/CD 集成

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub Actions](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>后续步骤

* [详细了解](container-registry-intro.md) Azure 容器注册表。
