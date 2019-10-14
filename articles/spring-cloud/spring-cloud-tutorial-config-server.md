---
title: 在 Azure Spring Cloud 中设置配置服务器 | Microsoft Docs
description: 本教程介绍如何在 Azure 门户上为 Azure Spring Cloud 设置 Spring Cloud 配置服务器
services: spring-cloud
ms.service: spring-cloud
ms.topic: tutorial
ms.reviewer: jeconnoc
ms.author: v-vasuke
author: v-vasuke
ms.date: 08/08/2019
ms.openlocfilehash: f959d216859ded54de52de474ee3150f91bcfff1
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038649"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-for-your-service"></a>教程：为服务设置 Spring Cloud 配置服务器

本教程介绍如何将 Spring Cloud 配置服务器连接到 Azure Spring Cloud 服务。

Spring Cloud Config 为分布式系统中的外部化配置提供服务器和客户端支持。 使用配置服务器可在一个中心位置管理所有环境中应用程序的外部属性。 有关详细信息，请访问 [Spring Cloud 配置服务器参考](https://spring.io/projects/spring-cloud-config)。

## <a name="prerequisites"></a>先决条件
* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
* 一个已预配的处于运行状态的 Azure Spring Cloud 服务。  完成[此快速入门](spring-cloud-quickstart-launch-app-cli.md)，了解如何预配并启动 Azure Spring Cloud 服务。


## <a name="restriction"></a>限制

在后端上使用__配置服务器__时存在一些限制。 某些属性将自动注入到应用程序环境，以访问__配置服务器__和__服务发现__。 如果你同时从**配置服务器**文件配置了这些属性，可能会遇到冲突和意外的行为。  属性包括： 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
```
> [!CAUTION]
> 我们强烈建议__不要__将上述属性放入__配置服务器__应用程序文件中。

## <a name="create-your-config-server-files"></a>创建配置服务器文件

Azure Spring Cloud 支持使用 Azure DevOps、GitHub、GitLab 和 Bitbucket 来存储配置服务器文件。  准备好存储库后，请按照以下说明创建配置文件，并将其存储到这些位置。

此外，某些可配置的属性仅适用于某些类型。 以下小节列出了每个存储库类型的属性。


### <a name="public-repository"></a>公共存储库

使用公共存储库时，可配置属性的受限程度更严重。

下面列出了用于设置公共 `Git` 存储库的所有可配置属性。

> [!NOTE]
> 目前仅支持连字符（“-”）分隔字命名约定。 例如，应使用 `default-label`，而不要使用 `defaultLabel`。

| 属性        | 必选 | Feature                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | 用作配置服务器后端的 `Git` 存储库的 `uri` 应以 `http://`、`https://`、`git@` 或 `ssh://` 开头。 |
| `default-label` | `no`     | `Git` 存储库的默认标签应为存储库的 `branch name`、`tag name` 或 `commit-id`。 |
| `search-paths`  | `no`     | 用于搜索 `Git` 存储库子目录的字符串数组。 |

------

### <a name="private-repository-with-ssh-authentication"></a>使用 SSH 身份验证的专用存储库

下面列出了用于设置使用 `Ssh` 的专用 `Git` 存储库的所有可配置属性。

> [!NOTE]
> 目前仅支持连字符（“-”）分隔字命名约定。 例如，应使用 `default-label`，而不要使用 `defaultLabel`。

| 属性                   | 必选 | Feature                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | `yes`    | 用作配置服务器后端的 `Git` 存储库的 `uri` 应以 `http://`、`https://`、`git@` 或 `ssh://` 开头。 |
| `default-label`            | `no`     | `Git` 存储库的默认标签应为存储库的 `branch name`、`tag name` 或 `commit-id`。 |
| `search-paths`             | `no`     | 用于搜索 `Git` 存储库子目录的字符串数组。 |
| `private-key`              | `no`     | 用于访问 `Git` 存储库的 `Ssh` 私钥，如果 `uri` 以 `git@` 或 `ssh://` 开头，则此属性是__必需的__。 |
| `host-key`                 | `no`     | Git 存储库服务器的主机密钥，不应包含 `host-key-algorithm` 涵盖的算法前缀。 |
| `host-key-algorithm`       | `no`     | 主机密钥算法，应为某个 `ssh-dss`。 `ssh-rsa`、`ecdsa-sha2-nistp256`、`ecdsa-sha2-nistp384` 和 `ecdsa-sha2-nistp521`。 仅当 `host-key` 存在时才是必需的。 |
| `strict-host-key-checking` | `no`     | 指示在利用提供的 `host-key` 时配置服务器是否无法启动，应为 `true`（默认值）或 `false`。 |

-----

### <a name="private-repository-with-basic-authentication"></a>使用基本身份验证的专用存储库

下面列出了用于设置使用基本身份验证的专用 Git 存储库的所有可配置属性。

> [!NOTE]
> 目前仅支持连字符（“-”）分隔字命名约定。 例如，可以使用 `default-label`，但不能使用 `defaultLabel`。

| 属性        | 必选 | Feature                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | 用作配置服务器后端的 `Git` 存储库的 `uri` 应以 `http://`、`https://`、`git@` 或 `ssh://` 开头。 |
| `default-label` | `no`     | `Git` 存储库的默认标签应为存储库的 `branch name`、`tag name` 或 `commit-id`。 |
| `search-paths`  | `no`     | 用于搜索 `Git` 存储库子目录的字符串数组。 |
| `username`      | `no`     | 用于访问 `Git` 存储库服务器的 `username`，如果 `Git` 存储库服务器支持 `Http Basic Authentication`，则此属性是__必需的__。 |
| `password`      | `no`     | 用于访问 `Git` 存储库服务器的密码，如果 `Git` 存储库服务器支持 `Http Basic Authentication`，则此属性是__必需的__。 |

> [!NOTE]
> 某些 `Git` 存储库服务器（例如 GitHub）支持使用“个人令牌”或“访问令牌”作为 `HTTP Basic Authentication` 的密码。 也可以在此处使用这种令牌作为密码；“个人令牌”或“访问令牌”不会过期。 但对于 BitBucket 和 Azure DevOps 之类的 Git 存储库服务器，令牌将在一到两个小时后过期，导致该选项不可继续用于 Azure Spring Cloud。

### <a name="git-repositories-with-pattern"></a>带模式的 Git 存储库

下面列出了用于设置带模式的 Git 存储库的所有可配置属性。

> [!NOTE]
> 目前仅支持连字符（“-”）分隔字命名约定。 例如，可以使用 `default-label`，但不能使用 `defaultLabel`。

| 属性                           | 必选         | Feature                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | `no`             | 映射由具有给定名称的 `Git` 存储库设置组成。 |
| `repos."uri"`                      | `repos` 上的 `yes` | 用作配置服务器后端的 `Git` 存储库的 `uri` 应以 `http://`、`https://`、`git@` 或 `ssh://` 开头。 |
| `repos."name"`                     | `repos` 上的 `yes` | 用于标识一个 `Git` 存储库的名称，仅当 `repos` 存在时才是__必需的__。 例如上面的 `team-A` 和 `team-B`。 |
| `repos."pattern"`                  | `no`             | 用于匹配应用程序名称的字符串数组，对于每个模式，将采用包含通配符的 `{application}/{profile}` 格式。 |
| `repos."default-label"`            | `no`             | `Git` 存储库的默认标签应为存储库的 `branch name`、`tag name` 或 `commit-id`。 |
| `repos."search-paths`"             | `no`             | 用于搜索 `Git` 存储库子目录的字符串数组。 |
| `repos."username"`                 | `no`             | 用于访问 `Git` 存储库服务器的 `username`，如果 `Git` 存储库服务器支持 `Http Basic Authentication`，则此属性是__必需的__。 |
| `repos."password"`                 | `no`             | 用于访问 `Git` 存储库服务器的密码，如果 `Git` 存储库服务器支持 `Http Basic Authentication`，则此属性是__必需的__。 |
| `repos."private-key"`              | `no`             | 用于访问 `Git` 存储库的 `Ssh` 私钥，如果 `uri` 以 `git@` 或 `ssh://` 开头，则此属性是__必需的__。 |
| `repos."host-key"`                 | `no`             | Git 存储库服务器的主机密钥，不应包含 `host-key-algorithm` 涵盖的算法前缀。 |
| `repos."host-key-algorithm"`       | `no`             | 主机密钥算法，应为某个 `ssh-dss`。 `ssh-rsa`、`ecdsa-sha2-nistp256`、`ecdsa-sha2-nistp384` 和 `ecdsa-sha2-nistp521`。 仅当 `host-key` 存在时才是__必需的__。 |
| `repos."strict-host-key-checking"` | `no`             | 指示在利用提供的 `host-key` 时配置服务器是否无法启动，应为 `true`（默认值）或 `false`。 |

### <a name="import-applicationyml-file-from-spring-cloud-config"></a>从 Spring Cloud Config 导入 `application.yml` 文件

可以直接从 [Spring Cloud Config](https://spring.io/projects/spring-cloud-config) 网站导入某些默认的配置服务器设置。 可以直接从 Azure 门户执行此操作，因此，现在无需执行任何步骤来准备配置服务器文件或存储库。

## <a name="attaching-your-config-server-repository-to-azure-spring-cloud"></a>将配置服务器存储库附加到 Azure Spring Cloud

将配置文件保存到存储库后，需要将 Azure Spring Cloud 连接到该存储库。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 导航到 Azure Spring Cloud 的“概述”页。 

1. 在左侧菜单中转到“设置”标题下的“配置服务器”选项卡。  

### <a name="public-repository"></a>公共存储库

如果存储库是公用的，则只需单击“公共”按钮并粘贴 URL。 

### <a name="private-repository"></a>专用存储库

Azure Spring Cloud 支持 SSH 身份验证。 按照 Azure 门户上的说明将公钥添加到存储库。 然后，确保在配置文件中包含私钥。

单击“应用”完成配置服务器的设置。 


## <a name="delete-your-app-configuration"></a>删除应用配置

保存配置文件后，“配置”选项卡中会显示“删除应用配置”按钮。   单击此按钮会彻底清除现有的设置。 如果你希望将配置服务器连接到另一个源（例如，从 GitHub 移到 Azure DevOps），则应执行此操作。

## <a name="next-steps"></a>后续步骤

本教程已介绍如何启用和配置配置服务器。 若要详细了解如何管理应用程序，请继续学习有关手动缩放应用的教程。

> [!div class="nextstepaction"]
> [了解如何手动缩放 Azure Spring Cloud 应用程序](spring-cloud-tutorial-scale-manual.md)。

