---
title: 使用 Azure Dev Space 时如何管理机密
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: 了解如何在使用 Azure 开发人员空间开发应用程序时在运行时使用 Kubernetes 机密或生成时间
keywords: Docker, Kubernetes, Azure, AKS, Azure 容器服务, 容器
ms.openlocfilehash: d9dd0de348612bbb3baf5fb351c1c9af1c228c1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438458"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>使用 Azure Dev Space 时如何管理机密

你的服务可能需要适用于某些服务（例如数据库或其他安全的 Azure 服务）的特定密码、连接字符串和其他机密。 在配置文件中设置这些机密的值即可让其以环境变量的形式在代码中使用。  必须小心处理这些配置文件，以避免危及机密的安全性。

## <a name="storing-and-using-runtime-secrets"></a>存储和使用运行时机密

Azure 开发人员空间提供了两个建议的简化选项，用于在 Azure 开发人员空间客户端工具生成的 Helm 图表中存储`values.dev.yaml`机密：在文件中，并直接`azds.yaml`在 中内联。 不建议在 中`values.yaml`存储机密。

> [!NOTE]
> 以下方法演示如何存储和使用客户端工具生成的 Helm 图表的机密。 如果您创建自己的 Helm 图表，则可以直接使用 Helm 图表来管理和存储机密。

### <a name="using-valuesdevyaml"></a>使用值.dev.yaml

在已使用 Azure 开发人员空间准备的项目中，在同一文件夹中创建`values.dev.yaml`一个文件`azds.yaml`，以定义密钥和值。 例如：

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

使用`azds.yaml`验证文件`values.dev.yaml`引用为可选。 `?` 例如：

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

如果您有其他机密文件，也可以在此处添加它们。

更新或验证服务将机密引用为环境变量。 例如：

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
使用`azds up`运行更新的服务。

```console
azds up
```
 
用于`kubectl`验证已创建您的机密。

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> 不建议将机密存储在源代码管理中。 如果使用 Git，请`values.dev.yaml`添加到`.gitignore`文件中以避免在源代码管理中提交机密。

### <a name="using-azdsyaml"></a>使用 azds.yaml

在已使用 Azure 开发人员空间准备的项目中，请使用*配置.dev.install.set*中的`azds.yaml`*$PLACEHOLDER*语法添加密钥和值。 例如：

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

> [!NOTE]
> 无需在 中使用`azds.yaml`*$PLACEHOLDER*语法，即可直接输入机密值。 但是，不建议使用此方法，因为`azds.yaml`此方法存储在源代码管理中。
     
在同一`.env`文件夹中创建一个文件`azds.yaml`，以定义 *$PLACEHOLDER*值。 例如：

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> 不建议将机密存储在源代码管理中。 如果使用 Git，请`.env`添加到`.gitignore`文件中以避免在源代码管理中提交机密。

更新或验证服务将机密引用为环境变量。 例如：

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
使用`azds up`运行更新的服务。

```console
azds up
```
 
用于`kubectl`验证已创建您的机密。

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>使用机密作为生成参数

上一节演示如何存储和使用在容器运行时使用的秘密。 您还可以在容器生成时使用任何机密，例如使用`azds.yaml`的专用 NuGet 的密码。

在`azds.yaml`中，使用语法设置*配置.开发.build.args*中的`<variable name>: ${secret.<secret name>.<secret key>}`生成时间机密。 例如：

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

在上面的示例中 *，mynuget 秘密*是现有机密，*而饼图尼*是现有密钥。

>[!NOTE]
> 机密名称和键可能包含该`.`字符。 用于`\`在将`.`机密作为生成参数传递时进行转义。 例如，传递一个名称为*foo.bar*的密钥，*token*该密钥`MYTOKEN: ${secret.foo\.bar.token}`具有令牌 ： 。 此外，可以使用前缀和后缀文本计算机密。 例如，`MYURL: eus-${secret.foo\.bar.token}-version1` 。 此外，父级和父级空间中可用的机密可以作为生成参数传递。

在 Dockerfile 中，使用*ARG*指令使用机密，然后在稍后的 Dockerfile 中使用同一变量。 例如：

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
 
