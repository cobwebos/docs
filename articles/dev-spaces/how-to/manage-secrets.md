---
title: 使用 Azure Dev Space 时如何管理机密
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: conceptual
description: 在 Azure 中使用容器和微服务快速开发 Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure 容器服务, 容器
ms.openlocfilehash: 900529d54a26729d9d0fb949d9217d5e2d618254
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515293"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>使用 Azure Dev Space 时如何管理机密

你的服务可能需要适用于某些服务（例如数据库或其他安全的 Azure 服务）的特定密码、连接字符串和其他机密。 在配置文件中设置这些机密的值即可让其以环境变量的形式在代码中使用。  必须小心处理，以免机密的安全性受损。

Azure 开发人员空间提供用于在 Azure 开发人员空间客户端工具生成的 Helm 图表中存储机密的两个建议、 更流畅地选项： 在此 values.dev.yaml 文件，并直接在 azds.yaml 中的内联。 建议不要将机密存储在 values.yaml 中。 外部 Helm 的两种方法生成客户端工具的图表中定义本文中，如果您创建 Helm 图表，您可以使用 Helm 图表直接用于管理和存储的机密。

## <a name="method-1-valuesdevyaml"></a>方法 1：values.dev.yaml
1. 使用为 Azure Dev Spaces 启用的项目打开 VS Code。
2. 添加名为的文件_values.dev.yaml_中相同的现有文件夹_azds.yaml_和定义的机密密钥和值，如以下示例所示：

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. _azds.yaml_已经引用_values.dev.yaml_文件存在。 如果需要不同的文件名称，更新 install.values 部分：

    ```yaml
    install:
      values:
      - values.dev.yaml?
      - secrets.dev.yaml?
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
                port: "$REDIS_PORT"
                host: "$REDIS_HOST"
                key: "$REDIS_KEY"
    ```
     
2.  在 _azds.yaml_ 所在的文件夹中创建 _.env_ 文件。 使用标准的“机密=值”表示法输入机密。 请勿将 _.env_ 文件提交到源代码管理。 （请将它添加到 _.gitignore_ 文件中，而不是基于 git 的版本控制系统的源代码管理中。）以下示例显示一个 _.env_ 文件：

    ```
    REDIS_PORT=3333
    REDIS_HOST=myredishost
    REDIS_KEY=myrediskey
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

通过这些方法，现在可以安全地连接到数据库、Azure Redis 缓存，或者访问安全的 Azure 服务。
 
