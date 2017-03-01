---
title: "使用 Azure 容器注册表进行身份验证 | Microsoft Docs"
description: "如何使用 Azure Active Directory 服务主体或管理员帐户来登录 Azure 容器注册表"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 128a937a-766a-415b-b9fc-35a6c2f27417
ms.service: container-registry
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
translationtype: Human Translation
ms.sourcegitcommit: 2a381431acb6436ddd8e13c69b05423a33cd4fa6
ms.openlocfilehash: 848a4011fd180721cf269390b8dd7426b2287dd2
ms.lasthandoff: 02/22/2017

---
# <a name="authenticate-with-a-container-registry"></a>使用容器注册表进行身份验证
若要在 Azure 容器注册表中使用容器映像，则使用 `docker login` 命令进行登录。 可使用 **[Azure Active Directory 服务主体](../active-directory/active-directory-application-objects.md)**或特定于注册表的**管理员帐户**登录。 本文提供了有关这些标识的详细信息。 


> [!NOTE]
> 容器注册表目前以预览版提供。 在预览版中，不支持单个 Azure Active Directory 标识（允许基于用户的访问和控制）使用容器注册表进行身份验证。 
> 





## <a name="service-principal"></a>服务主体

可向注册表[分配服务主体](container-registry-get-started-azure-cli.md#assign-a-service-principal)将其用于基本 Docker 身份验证。 建议在大多数情况下使用服务主体。 向 `docker login` 命令提供服务主体的应用 ID 和密码，如以下示例所示：

```
docker login myregistry-contoso.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

登录后，Docker 会缓存凭据，因此无需记住应用 ID。

> [!TIP]
> 如需要，可通过运行 `az ad sp reset-credentials` 命令重新生成服务主体的密码。
> 


服务主体允许对注册表[基于角色的访问](../active-directory/role-based-access-control-configure.md)。 可用角色为读取器（仅限拉取的访问）、参与者（拉取和推送）以及所有者（拉取、推送和将角色分配给其他用户）。 可将多个服务主体分配到注册表中，这样就可为不同的用户或应用程序定义访问权限。 服务主体也在开发人员或 DevOps 方案中启用到注册表的“无外设”连接，如下所示：

  * 从注册表到业务流程系统的容器部署（包括 DC/OS、Docker Swarm 和 Kubernetes）。 还可将容器注册表拉取到相关 Azure 服务，例如[容器服务](../container-service/index.md)、[应用服务](../app-service/index.md)、[Batch](../batch/index.md) 和 [Service Fabric](../service-fabric/index.md)。
  
  * 构建容器映像并将它们推送到注册表的持续集成和部署解决方案（例如 Visual Studio Team Services 或 Jenkins）。
  
  



## <a name="admin-account"></a>管理员帐户
每创建一个注册表，将自动创建一个管理员帐户。 默认情况下，帐户已禁用，但可通过[门户](container-registry-get-started-portal.md#manage-registry-settings)或使用 [Azure CLI 2.0 命令](container-registry-get-started-azure-cli.md#manage-admin-credentials)等启用它并管理凭据。 如果已启用帐户，则可将用户名和密码传递到 `docker login` 命令，对注册表进行基本身份验证。 例如：

```
docker login myregistry-contoso.azurecr.io -u myAdminName -p myPassword
```

> [!IMPORTANT]
> 管理员帐户专门用于单个用户访问注册表，主要用于测试目的。 不建议与其他用户共享管理员帐户凭据。 所有用户对于注册表都显示为单个用户。 更改或禁用此帐户会禁用所有使用凭据的用户的注册表访问权限。 
> 


### <a name="next-steps"></a>后续步骤
* [使用 Docker CLI 推送第一个映像](container-registry-get-started-docker-cli.md)。
* 有关容器注册表预览版中身份验证的详细信息，请参阅[博客文章](https://blogs.msdn.microsoft.com/stevelasker/2016/11/17/azure-container-registry-user-accounts/)。 



