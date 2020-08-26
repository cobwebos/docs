---
title: 导入容器映像
description: 使用 Azure API 将容器映像导入到 Azure 容器注册表中，无需运行 Docker 命令。
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 66c3a8b19e2288c1f8720dd4fe79f348a11f052e
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660489"
---
# <a name="import-container-images-to-a-container-registry"></a>向容器注册表导入容器映像

可以轻松将容器映像导入（复制）到 Azure 容器注册表中，无需使用 Docker 命令。 例如，将映像从开发注册表导入到生产注册表，或者从公共注册表复制基础映像。

Azure 容器注册表可灵活应对许多常见方案，以便从现有注册表复制映像：

* 从公共注册表导入

* 在同一或不同 Azure 订阅中从另一个 Azure 容器注册表导入

* 从非 Azure 专用容器注册表导入

与使用 Docker CLI 命令相比，将映像导入到 Azure 容器注册表具有以下优点：

* 由于客户端环境不需要本地 Docker 安装，因此无需考虑受支持的 OS 类型即可导入任何容器映像。

* 导入多体系结构映像（例如正式的 Docker 映像）时，会复制清单列表中指定的所有体系结构和平台的映像。

* 访问源和目标注册表不必使用注册表的公共终结点。

若要导入容器映像，本文要求在 Azure Cloud Shell 中或本地（建议使用 2.0.55 或更高版本）运行 Azure CLI。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。

> [!NOTE]
> 如果需要在多个 Azure 区域中分布相同的容器映像，则 Azure 容器注册表还支持[异地复制](container-registry-geo-replication.md)。 通过异地复制注册表 (需要高级服务层) ，你可以使用一个注册表中具有相同映像和标记名称的多个区域。
>

## <a name="prerequisites"></a>先决条件

如果还没有 Azure 容器注册表，请创建注册表。 有关步骤，请参阅 [快速入门：使用 Azure CLI 创建专用容器注册表](container-registry-get-started-azure-cli.md)。

若要将映像导入到 Azure 容器注册表，你的标识必须对目标注册表具有写入权限， (至少是参与者角色，或者是允许 importImage 操作) 的自定义角色。 请参阅 [Azure 容器注册表角色和权限](container-registry-roles.md#custom-roles)。 

## <a name="import-from-a-public-registry"></a>从公共注册表导入

### <a name="import-from-docker-hub"></a>从 Docker 中心导入

例如，使用 [az acr import][az-acr-import] 命令将多体系结构 `hello-world:latest` 映像从 Docker 中心导入到名为 myregistry 的注册表**。 由于 `hello-world` 是来自 Docker 中心的官方映像，因此该映像位于默认的 `library` 存储库中。 `--source` 映像参数的值中包含存储库名称和（可选）标记。 （可以选择性根据映像的清单摘要而不是标签来标识映像，这确保映像为特定版本。）
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

可以通过运行 `az acr repository show-manifests` 命令来验证多个清单是否与此映像关联：

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

下面的示例从 Docker 中心中的 `tensorflow` 存储库导入公共映像：

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>从 Microsoft 容器注册表导入

例如， `ltsc2019` 从 `windows` Microsoft 容器注册表中的存储库导入 Windows Server Core 映像。

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:ltsc2019 \
--image servercore:ltsc2019
```

## <a name="import-from-another-azure-container-registry"></a>从另一 Azure 容器注册表导入

可以使用集成的 Azure Active Directory 权限从另一 Azure 容器注册表导入映像。

* 你的标识必须具有 Azure Active Directory 的权限，才能读取源注册表 (读取者角色) ，并导入到目标注册表 (参与者角色，或允许 importImage 操作) 的 [自定义角色](container-registry-roles.md#custom-roles) 。

* 注册表可以位于同一 Active Directory 租户的同一或不同 Azure 订阅中。

* 可以禁用对源注册表的[公共访问](container-registry-access-selected-networks.md#disable-public-network-access)。 如果禁用公共访问，请按资源 ID 而不是注册表登录服务器名称指定源注册表。

### <a name="import-from-a-registry-in-the-same-subscription"></a>从同一订阅的注册表中导入

例如，在同一 Azure 订阅中，将 `aci-helloworld:latest` 映像从源注册表 mysourceregistry 导入到 myregistry****。

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

下面的示例将 `aci-helloworld:latest` 映像从 *myregistry* 的源注册表 *mysourceregistry* 导入到禁用了对注册表的公共终结点的访问。 使用 `--registry` 参数提供源注册表的资源 ID。 请注意， `--source` 参数仅指定源存储库和标记，而不指定注册表登录服务器名称。

```azurecli
az acr import \
  --name myregistry \
  --source aci-helloworld:latest \
  --image aci-helloworld:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

下面的示例通过清单摘要（SHA-256 哈希代码，表示为 `sha256:...`）而非标记导入映像：

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>从不同订阅的注册表导入

在下面的示例中，mysourceregistry 与 myregistry 处于同一 Active Directory 租户的不同订阅中****。 使用 `--registry` 参数提供源注册表的资源 ID。 请注意， `--source` 参数仅指定源存储库和标记，而不指定注册表登录服务器名称。

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>使用服务主体凭据从注册表导入

若要使用 Active Directory 权限从无法访问的注册表导入，可以使用服务主体凭据（如果可用）。 提供对源注册表具有 ACRPull 访问权限的 Active Directory [服务主体](container-registry-auth-service-principal.md)的 appID 和密码。 服务主体适用于需将映像导入到注册表的生成系统和其他无人参与系统。

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  –-password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>从非 Azure 专用容器注册表导入

通过指定启用对注册表的拉取访问的凭据，从专用注册表导入映像。 例如，从专用 Docker 注册表拉取映像： 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>后续步骤

在本文中，你了解了如何从公共注册表或其他专用注册表将容器映像导入 Azure 容器注册表。 关于其他映像导入选项，请参阅 [az acr import][az-acr-import] 命令参考。 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
