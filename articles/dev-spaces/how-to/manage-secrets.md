---
title: 使用 Azure Dev Space 时如何管理机密 | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
ms.technology: azds-kubernetes
description: 在 Azure 中使用容器和微服务快速开发 Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure 容器服务, 容器
manager: douge
ms.openlocfilehash: b77d862f578ddc374dbb58117b4ea58eb973e5fe
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198307"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>使用 Azure Dev Space 时如何管理机密

你的服务可能需要适用于某些服务（例如数据库或其他安全的 Azure 服务）的特定密码、连接字符串和其他机密。 在配置文件中设置这些机密的值即可让其以环境变量的形式在代码中使用。  必须小心处理，以免机密的安全性受损。

Azure Dev Spaces 提供两个建议用于存储机密的选项：一是存储在 values.dev.yaml 文件中，二是以内联方式直接存储在 azds.yaml 中。 建议不要将机密存储在 values.yaml 中。
 
## <a name="method-1-valuesdevyaml"></a>方法 1：values.dev.yaml
1. 使用为 Azure Dev Spaces 启用的项目打开 VS Code。
2. 在现有的 _values.yaml_ 所在的文件夹中添加名为 _values.dev.yaml_ 的文件，然后定义密钥和值，如以下示例所示：

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. 更新 _azds.yaml_，告知 Azure Dev Spaces 使用新的 _values.dev.yaml_ 文件。 为此，请将此配置添加到 configurations.develop.container 部分下：

    ```yaml
           container:
             values:
             - "charts/webfrontend/values.dev.yaml"
    ```
 
4. 修改服务代码，将这些机密称为环境变量，如以下示例所示：

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
    
5. 使用这些更改更新在群集中运行的服务。 在命令行上运行以下命令：

    ```
    azds up
    ```
 
6. （可选）在命令行中检查是否已创建这些机密：

      ```
      kubectl get secret --namespace default -o yaml 
      ```

7. 确保将 _values.dev.yaml_ 添加到 _.gitignore_ 文件，避免在源代码管理中提交机密。
 
 
## <a name="method-2-inline-directly-in-azdsyaml"></a>方法 2：以内联方式直接存储在 azds.yaml 中
1.  在 _azds.yaml_ 的 yaml 节 configurations/develop/install 下设置机密。 虽然可以直接在该处输入机密值，但建议不要这样做，因为 _azds.yaml_ 会签入源代码管理中。 请改用 "$PLACEHOLDER" 语法来添加占位符。

    ```yaml
    configurations:
      develop:
        ...
        install:
          set:
            secrets:
              redis:
                port: "$REDIS_PORT_DEV"
                host: "$REDIS_HOST_DEV"
                key: "$REDIS_KEY_DEV"
    ```
     
2.  在 _azds.yaml_ 所在的文件夹中创建 _.env_ 文件。 使用标准的“机密=值”表示法输入机密。 请勿将 _.env_ 文件提交到源代码管理。 （请将它添加到 _.gitignore_ 文件中，而不是基于 git 的版本控制系统的源代码管理中。）以下示例显示一个 _.env_ 文件：

    ```
    REDIS_PORT_DEV=3333
    REDIS_HOST_DEV=myredishost
    REDIS_KEY_DEV=myrediskey
    ```
2.  修改服务源代码，以便在代码中引用这些机密，如以下示例所示：

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
 
3.  使用这些更改更新在群集中运行的服务。 在命令行上运行以下命令：

    ```
    azds up
    ```

4.  （可选）通过 kubectl 查看机密：

    ```
    kubectl get secret --namespace default -o yaml
    ```

## <a name="next-steps"></a>后续步骤

现在可以使用这些方法安全地连接到数据库或 Redis 缓存，或者访问安全的 Azure 服务。
 