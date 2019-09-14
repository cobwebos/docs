---
title: Azure 容器注册表中的内容信任
description: 了解如何为 Azure 容器注册表启用内容信任，以及如何推送和拉取签名的映像。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 09/06/2019
ms.author: danlep
ms.openlocfilehash: c0d4bd397c68fe3ed2d36404af9230e2316f3362
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70959184"
---
# <a name="content-trust-in-azure-container-registry"></a>Azure 容器注册表中的内容信任

Azure 容器注册表实现了 Docker 的[内容信任][docker-content-trust]模型，支持推送和拉取已签名的映像。 本文可帮助你开始在容器注册表中启用内容信任。

> [!NOTE]
> 内容信任是 Azure 容器注册表[高级 SKU](container-registry-skus.md) 的一项功能。

## <a name="how-content-trust-works"></a>内容信任工作原理

对于在设计时需考虑安全性的任何分布式系统来说，重要的是验证进入系统的数据的源和完整性。 数据的使用者需要能够验证数据的发布者（源），并确保数据在发布后未修改过（完整性）。 

作为映像发布者，你可以通过内容信任对发布到注册表的映像进行**签名**。 映像的使用者（从注册表拉取映像的人或系统）可以将其客户端配置为仅拉取签名的映像。 当映像使用者拉取签名的映像时，其 Docker 客户端会验证映像的完整性。 在此模型中，可以向使用者确保你注册表中的签名映像确实是你发布的，且自发布后未作修改。

### <a name="trusted-images"></a>受信任的映像

内容信任使用存储库中的**标记**。 映像存储库包含的映像可以带签名的和未签名的标记。 例如，可以只对 `myimage:stable` 和 `myimage:latest` 映像签名，而不对 `myimage:dev` 映像签名。

### <a name="signing-keys"></a>签名密钥

内容信任通过一组加密签名密钥来管理。 这些密钥与注册表中的特定存储库相关联。 Docker 客户端和你的注册表使用多种类型的签名密钥来管理存储库中标记的信任。 启用内容信任并将其集成到容器发布和使用管道中时，必须小心管理这些密钥。 有关详细信息，请参阅本文后面的[密钥管理](#key-management)以及 Docker 文档中的[管理内容信任的密钥][docker-manage-keys]。

> [!TIP]
> 这是对 Docker 的内容信任模型的简要概述。 若要深入探讨内容信任，请参阅 [Docker 中的内容信任][docker-content-trust]。

## <a name="enable-registry-content-trust"></a>启用注册表内容信任

第一步是在注册表级别启用内容信任。 启用内容信任以后，客户端（用户或服务）即可将签名的映像推送到注册表。 在注册表上启用内容信任并不意味着只能由启用了内容信任的使用者来使用注册表。 未启用内容信任的使用者可以照常继续使用注册表。 不过，在其客户端中启用了内容信任的使用者在其注册表中只能看到签名的映像。

若要为注册表启用内容信任，请先在 Azure 门户中导航到注册表。 在“策略”下选择“内容信任” > “启用” > “保存”。 你还可以在 Azure CLI 中使用[az acr config content trust update][az-acr-config-content-trust-update]命令。

![在 Azure 门户中为注册表启用内容信任][content-trust-01-portal]

## <a name="enable-client-content-trust"></a>启用客户端内容信任

若要使用受信任的映像，映像发布者和使用者均需为其 Docker 客户端启用内容信任。 作为发布者，你可以为推送到启用了内容信任的注册表的映像签名。 作为使用者，你在启用内容信任限制后只能查看注册表的签名映像。 内容信任在 Docker 客户端中默认禁用，但可以按 shell 会话或按命令启用。

若要为 shell 会话启用内容信任，请将 `DOCKER_CONTENT_TRUST` 环境变量设置为 **1**。 例如，在 Bash shell 中，请执行以下代码：

```bash
# Enable content trust for shell session
export DOCKER_CONTENT_TRUST=1
```

若要为单个命令启用或禁用内容信任，可以使用多个 Docker 命令支持的 `--disable-content-trust` 参数。 若要为单个命令启用内容信任，请执行以下代码：

```bash
# Enable content trust for single command
docker build --disable-content-trust=false -t myacr.azurecr.io/myimage:v1 .
```

如果已经为 shell 会话启用了内容信任，希望对单个命令禁用它，请执行以下代码：

```bash
# Disable content trust for single command
docker build --disable-content-trust -t myacr.azurecr.io/myimage:v1 .
```

## <a name="grant-image-signing-permissions"></a>授予映像签名权限

只有已获得授权的用户或系统能够向注册表推送受信任的映像。 若要向用户（或使用服务主体的系统）授予受信任映像的推送权限，请为其 Azure Active Directory 标识授予 `AcrImageSigner` 角色。 这是在 `AcrPush`（或等效）角色（此角色是将映像推送到注册表所必需的）基础上添加的角色。 有关详细信息，请参阅 [Azure 容器注册表角色和权限](container-registry-roles.md)。

> [!NOTE]
> 不能将受信任的映像推送权限授予 Azure 容器注册表的[管理员帐户](container-registry-authentication.md#admin-account)。

下面是在 Azure 门户和 Azure CLI 中授予 `AcrImageSigner` 角色的详细信息。

### <a name="azure-portal"></a>Azure 门户

导航到 Azure 门户中的注册表，然后选择“访问控制(IAM)” > “添加角色分配”。 在“添加角色分配”下的“角色”下选择“`AcrImageSigner`”，然后**选择**一个或多个用户或服务主体，然后单击“保存”。

在此示例中，为以下两个实体分配了 `AcrImageSigner` 角色：名为“service-principal”的服务主体，以及名为“Azure 用户”的用户。

![在 Azure 门户中为注册表启用内容信任][content-trust-02-portal]

### <a name="azure-cli"></a>Azure CLI

若要通过 Azure CLI 向用户授予签名权限，请为用户分配局限于注册表的 `AcrImageSigner` 角色。 该命令的格式如下：

```azurecli
az role assignment create --scope <registry ID> --role AcrImageSigner --assignee <user name>
```

例如，若要将该角色授予自己，可以在经身份验证的 Azure CLI 会话中运行以下命令。 修改 `REGISTRY` 值，使之反映 Azure 容器注册表的名称。

```bash
# Grant signing permissions to authenticated Azure CLI user
REGISTRY=myregistry
USER=$(az account show --query user.name --output tsv)
REGISTRY_ID=$(az acr show --name $REGISTRY --query id --output tsv)

az role assignment create --scope $REGISTRY_ID --role AcrImageSigner --assignee $USER
```

也可向[服务主体](container-registry-auth-service-principal.md)授予将受信任的映像推送到注册表的权限。 服务主体适用于需将受信任的映像推送到注册表的生成系统和其他无人参与系统。 格式与授予用户权限类似，但需为 `--assignee` 值指定一个服务主体 ID。

```azurecli
az role assignment create --scope $REGISTRY_ID --role AcrImageSigner --assignee <service principal ID>
```

`<service principal ID>` 可以是服务主体的 **appId**、**objectId** 或其 **servicePrincipalName** 之一。 若要详细了解如何使用服务主体和 Azure 容器注册表，请参阅[使用服务主体的 Azure 容器注册表身份验证](container-registry-auth-service-principal.md)。

> [!IMPORTANT]
> 在任何角色更改后，运行 `az acr login` 以刷新 Azure CLI 的本地标识令牌，以便新角色生效。 有关验证身份的角色的信息，请参阅[使用 rbac 和 Azure CLI 管理对 azure 资源的访问](../role-based-access-control/role-assignments-cli.md)和[排查 AZURE 资源的 RBAC 问题](../role-based-access-control/troubleshooting.md)。

## <a name="push-a-trusted-image"></a>推送受信任的映像

若要将受信任的映像标记推送到容器注册表，请启用内容信任并使用 `docker push` 推送映像。 首次推送签名的标记时，系统会要求你为根签名密钥和存储库签名密钥创建通行短语。 根密钥和存储库密钥都是以本地方式在计算机上生成并存储的。

```console
$ docker push myregistry.azurecr.io/myimage:v1
The push refers to repository [myregistry.azurecr.io/myimage]
ee83fc5847cb: Pushed
v1: digest: sha256:aca41a608e5eb015f1ec6755f490f3be26b48010b178e78c00eac21ffbe246f1 size: 524
Signing and pushing trust metadata
You are about to create a new root signing key passphrase. This passphrase
will be used to protect the most sensitive key in your signing system. Please
choose a long, complex passphrase and be careful to keep the password and the
key file itself secure and backed up. It is highly recommended that you use a
password manager to generate the passphrase and keep it safe. There will be no
way to recover this key. You can find the key in your config directory.
Enter passphrase for new root key with ID 4c6c56a:
Repeat passphrase for new root key with ID 4c6c56a:
Enter passphrase for new repository key with ID bcd6d98:
Repeat passphrase for new repository key with ID bcd6d98:
Finished initializing "myregistry.azurecr.io/myimage"
Successfully signed myregistry.azurecr.io/myimage:v1
```

首次在启用内容信任的情况下执行 `docker push` 后，Docker 客户端会使用同一根密钥进行后续推送。 每次将内容后续推送到同一存储库时，系统只要求提供存储库密钥。 每次将受信任的映像推送到新存储库时，系统会要求提供新存储库密钥的通行短语。

## <a name="pull-a-trusted-image"></a>拉取受信任的映像

若要拉取受信任的映像，请照常启用内容信任并运行 `docker pull` 命令。 若要拉取受信任的映像，`AcrPull` 角色对于普通用户来说已足够了。 无需任何其他角色（如 `AcrImageSigner` 角色）。 在启用内容信任的情况下，使用者只能拉取带签名标记的映像。 下面是拉取签名标记的示例：

```console
$ docker pull myregistry.azurecr.io/myimage:signed
Pull (1 of 1): myregistry.azurecr.io/myimage:signed@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b: Pulling from myimage
8e3ba11ec2a2: Pull complete
Digest: sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
Status: Downloaded newer image for myregistry.azurecr.io/myimage@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
Tagging myregistry.azurecr.io/myimage@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b as myregistry.azurecr.io/myimage:signed
```

如果客户端在启用内容信任的情况下尝试拉取未签名的标记，则操作会失败：

```console
$ docker pull myregistry.azurecr.io/myimage:unsigned
No valid trust data for unsigned
```

### <a name="behind-the-scenes"></a>幕后

运行 `docker pull` 时，Docker 客户端使用与 [Notary CLI][docker-notary-cli] 中的库相同的库为要拉取的标记请求 tag-to-SHA-256 摘要映射。 在验证信任数据上的签名以后，客户端会指示 Docker 引擎执行“按摘要进行的拉取”操作。 在拉取过程中，引擎使用 SHA-256 校验和作为内容地址，以便从 Azure 容器注册表请求映像清单并对其进行验证。

## <a name="key-management"></a>密钥管理

如推送第一个受信任映像时的 `docker push` 输出中所述，根密钥是最敏感的。 请确保备份根密钥并将其存储在安全的位置。 默认情况下，Docker 客户端将签名密钥存储在以下目录中：

```sh
~/.docker/trust/private
```

通过将根密钥和存储库密钥压缩到存档中并将其存储在安全位置来备份它们。 例如，在 Bash 中执行以下代码：

```bash
umask 077; tar -zcvf docker_private_keys_backup.tar.gz ~/.docker/trust/private; umask 022
```

在推送受信任的映像时，除了本地生成的根密钥和存储库密钥，Azure 容器注册表还会生成并存储多个其他的密钥。 若要详细讨论 Docker 的内容信任实施中的各种密钥，包括其他管理指南，请参阅 Docker 文档中的[管理内容信任的密钥][docker-manage-keys]。

### <a name="lost-root-key"></a>丢失根密钥

如果无法访问根密钥，则无法访问其标记使用该密钥签名的任何存储库中的签名标记。 如果映像标记是使用已丢失根密钥签名的，则 Azure 容器注册表无法还原对此类标记的访问权限。 若要删除注册表的所有信任数据（签名），请先禁用注册表的内容信任，然后再重新启用它。

> [!WARNING]
> 如果在禁用注册表中的内容信任后重新启用它，**则会删除注册表的所有存储库中所有已签名标记的所有信任数据**。 此操作不可逆--Azure 容器注册表不能恢复已删除的信任数据。 禁用内容信任不会删除映像本身。

若要禁用注册表的内容信任，请在 Azure 门户中导航到注册表。 在“策略”下选择“内容信任” > “禁用” > “保存”。 系统会警告：你会丢失注册表中的所有签名。 选择“确定”即可永久删除注册表中的所有签名。

![在 Azure 门户中禁用注册表的内容信任][content-trust-03-portal]

## <a name="next-steps"></a>后续步骤

* 有关内容信任的其他信息，请参阅 [Docker 中的内容信任][docker-content-trust]。 虽然本文介绍了一些要点，但内容信任是一个范围广泛的主题，若要深入进行了解，请查看 Docker 文档。

* 有关在生成和推送 Docker 映像时使用内容信任的示例，请参阅 [Azure Pipelines](/azure/devops/pipelines/build/content-trust) 文档。

<!-- IMAGES> -->
[content-trust-01-portal]: ./media/container-registry-content-trust/content-trust-01-portal.png
[content-trust-02-portal]: ./media/container-registry-content-trust/content-trust-02-portal.png
[content-trust-03-portal]: ./media/container-registry-content-trust/content-trust-03-portal.png

<!-- LINKS - external -->
[docker-content-trust]: https://docs.docker.com/engine/security/trust/content_trust
[docker-manage-keys]: https://docs.docker.com/engine/security/trust/trust_key_mng/
[docker-notary-cli]: https://docs.docker.com/notary/getting_started/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-content-trust-update]: /cli/azure/acr/config/content-trust#az-acr-config-content-trust-update
