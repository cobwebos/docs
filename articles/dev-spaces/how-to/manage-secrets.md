---
title: 使用 Azure Dev Space 时如何管理机密
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: 使用 Azure 上的容器和微服务进行快速的 Kubernetes 开发
keywords: Docker, Kubernetes, Azure, AKS, Azure 容器服务, 容器
ms.openlocfilehash: b184f72dfbbfe093443ab8a9b79bafbece3a3d51
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790167"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>使用 Azure Dev Space 时如何管理机密

你的服务可能需要适用于某些服务（例如数据库或其他安全的 Azure 服务）的特定密码、连接字符串和其他机密。 在配置文件中设置这些机密的值即可让其以环境变量的形式在代码中使用。  必须小心处理，以免机密的安全性受损。

Azure Dev Spaces 提供了两个建议的简化选项，用于在 Azure Dev Spaces 客户端工具生成的 Helm 图表中存储机密：在 `values.dev.yaml` 文件中，并直接在 `azds.yaml`中内联。 不建议在 `values.yaml`中存储机密。 在本文中定义的客户端工具所生成的两种 Helm 图方法之外，如果您创建自己的 Helm 图表，则可以直接使用 Helm 图表来管理和存储机密。

## <a name="method-1-valuesdevyaml"></a>方法 1：values.dev.yaml
1. 使用为 Azure Dev Spaces 启用的项目打开 VS Code。
2. 将名为_yaml_的文件添加到与现有_yaml_相同的文件夹中，并定义机密密钥和值，如下例所示：

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. _azds yaml_已引用_yaml_文件（如果存在）。 如果希望使用不同的文件名，请更新 install 部分：

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
 
 
## <a name="method-2-azdsyaml"></a>方法2： azds. yaml
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
     
2.  在 _azds.yaml_ 所在的文件夹中创建 _.env_ 文件。 使用标准的“机密=值”表示法输入机密。 请勿将 _.env_ 文件提交到源代码管理。 （若要在基于 git 的版本控制系统中的源代码管理中省略，请将其添加到 _.gitignore_文件中。）下面的示例演示了一个_env_文件：

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

## <a name="passing-secrets-as-build-arguments"></a>将机密作为生成参数传递

前面几节介绍了如何在容器运行时传递要使用的机密。 你还可以使用 `azds.yaml`在容器构建时传递机密，如专用 NuGet 的密码。

在 `azds.yaml`中，使用 `<variable name>: ${secret.<secret name>.<secret key>}` 语法在*配置*中设置生成时间机密。 例如：

```yaml
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
        MYTOKEN: ${secret.mynugetsecret.pattoken}
```

在上面的示例中， *mynugetsecret*是一个现有的机密， *pattoken*是一个现有密钥。

>[!NOTE]
> 机密名称和密钥可能包含 `.` 字符。 将机密作为生成参数传递时，使用 `\` 对 `.` 进行转义。 例如，若要传递名为*foo*的机密，请使用*令牌*： `MYTOKEN: ${secret.foo\.bar.token}`。 此外，还可以通过前缀和后缀文本来评估机密。 例如，`MYURL: eus-${secret.foo\.bar.token}-version1` 。 此外，可以将父代和祖父空间中提供的机密作为生成参数进行传递。

在 Dockerfile 中，使用*ARG*指令来使用机密，并稍后在 Dockerfile 中使用该相同的变量。 例如：

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

使用这些更改更新在群集中运行的服务。 在命令行上运行以下命令：

```
azds up
```

## <a name="next-steps"></a>后续步骤

通过这些方法，现在可以安全地连接到数据库、Azure Redis 缓存，或者访问安全的 Azure 服务。
 
