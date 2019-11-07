---
title: az 春季 cloud
description: 使用 Azure CLI 管理 Azure 春季云
author: jpconnock
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: jeconnoc
ms.openlocfilehash: a1f82d4efa7756b44ca5ed9859aa872c1f55b565
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607824"
---
# <a name="az-spring-cloud"></a>az 春季-cloud

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>使用 Azure CLI 管理 Azure 春季云

>[!Note]
> Azure 春季 Cloud 目前处于预览阶段。  将来的版本中可能会更改或删除这些命令。

| az 春季-cloud |  |
|------|------:|
| [az 春季-cloud create](#az-spring-cloud-create) | 创建 Azure 春季云实例。 |
| [az 春季-cloud delete](#az-spring-cloud-delete) | 删除 Azure 春季云实例。 |
| [az 春季-cloud list](#az-spring-cloud-list) | 列出给定资源组中的所有 Azure 春季 Cloud 实例，否则列出订阅 Id。 |
| [az 春季-cloud show](#az-spring-cloud-show) | 显示 Azure 春季云的详细信息。 |

| az 春季-cloud app | 用于在 Azure 春季云中管理应用的命令。  |
| ---- | ----: |
| [az 春季-cloud app create](#az-spring-cloud-app-create) | 使用 Azure 春季云中的默认部署创建新应用。 |
| [az 春季-cloud app delete](#az-spring-cloud-app-delete) | 删除 Azure 春季云中的应用。 |
| [az 春季-cloud app deploy](#az-spring-cloud-app-deploy) | 从源代码或预生成的二进制文件部署到应用并更新相关配置。 |
| [az 春季-cloud app list](#az-spring-cloud-app-list) | 列出 Azure 春季云中的所有应用。 |
| [az 春季-cloud app restart](#az-spring-cloud-app-restart) | 使用生产部署默认值重新启动应用的实例。 |
| [az 春季-cloud app scale](#az-spring-cloud-app-scale) | 手动缩放应用或其部署。 |
| [az 弹簧-云应用集-部署](#az-spring-cloud-app-set-deployment) | 设置应用的生产部署。 |
| [az 春季-cloud app show](#az-spring-cloud-app-show) | 在 Azure 春季云中显示应用的详细信息。 |
| [az 春季-cloud app show-deploy-log](#az-spring-cloud-app-show-deploy-log) | 显示来自源的最新部署的生成日志。 默认为生产部署。 |
| [az 春季-cloud app start](#az-spring-cloud-app-start) | 使用生产部署默认值启动应用的实例。 |
| [az 春季-cloud app stop](#az-spring-cloud-app-stop) | 使用生产部署默认值停止应用的实例。 |
| [az 春季-cloud app update](#az-spring-cloud-app-update) | 更新指定应用的配置。 |

| az 弹簧-云应用绑定 | 用于管理 Azure 数据服务绑定的命令。  必须重新启动该应用，这些设置才会生效。 |
| --- | ---: |
| [az 弹簧-云应用绑定列表](#az-spring-cloud-app-binding-list) | 列出应用中的所有服务绑定。 |
| [az 弹簧-云应用绑定删除](#az-spring-cloud-app-binding-remove) | 从应用中删除服务绑定。 |
| [az 春季-cloud app binding show](#az-spring-cloud-app-binding-show) | 显示服务绑定的详细信息。 |
| [az 春季-cloud app binding cosmos add](#az-spring-cloud-app-binding-cosmos-add) | 将 Azure CosmosDB 与应用绑定。 |
| [az 春季-cloud app binding cosmos update](#az-spring-cloud-app-binding-cosmos-update) | 更新 Azure CosmosDB 服务绑定。 |
| [az 春季-云应用绑定 mysql 添加](#az-spring-cloud-app-binding-mysql-add) | 将 Azure Database for MySQL 与应用绑定。 |
| [az 春季-cloud app binding mysql update](#az-spring-cloud-app-binding-mysql-update) | 更新 Azure Database for MySQL 服务绑定。 |
| [az 春季-cloud app binding redis add](#az-spring-cloud-app-binding-redis-add) | 将用于 Redis 的 Azure 缓存与应用绑定。 |
| [az 春季-cloud app binding redis update](#az-spring-cloud-app-binding-redis-update) | 更新用于 Redis 服务绑定的 Azure Cache。 |

| az 春季-cloud app deployment | 用于管理 Azure 春季云中应用的部署生命周期的命令。 |
| --- | ---: |
| [az 春季-cloud app deployment create](#az-spring-cloud-app-deployment-create) | 创建应用的过渡部署。 |
| [az 春季-cloud app deployment delete](#az-spring-cloud-app-deployment-delete) | 删除应用的部署。 |
| [az 春季-cloud app deployment list](#az-spring-cloud-app-deployment-list) | 列出应用中的所有部署。 |
| [az 春季-cloud app deployment show](#az-spring-cloud-app-deployment-show) | 显示部署的详细信息。 |

| az 春季-cloud config-server | 用于管理 Azure 春季 Cloud Config 服务器的命令。 |
| --- | ---: |
| [az 春季-cloud config-server clear](#az-spring-cloud-config-server-clear) | 清除配置服务器中的所有设置。 |
| [az 春季-云 config-服务器集](#az-spring-cloud-config-server-set) | 从 YAML 文件定义配置服务器。 |
| [az 春季-cloud config-server show](#az-spring-cloud-config-server-show) | 显示配置服务器配置。 |
| [az 春季-cloud config server git set](#az-spring-cloud-config-server-git-set) | 定义配置服务器的 git 属性。  将覆盖以前的值。 |
| [az 春季-cloud config server git 存储库添加](#az-spring-cloud-config-server-git-repo-add) | 将新的 git 存储库配置添加到配置服务器。 |
| [az 春季-cloud config server git 存储库列表](#az-spring-cloud-config-server-git-repo-list) | 列出配置服务器的所有 git 存储库配置。 |
| [az 春季-cloud config server git 存储库删除](#az-spring-cloud-config-server-git-repo-remove) | 从配置服务器中删除指定的 git 存储库。 |

| az 弹簧-云测试-终结点 | 用于管理 Azure 春季云中的终结点测试的命令 |
| --- | ---: |
| [az 弹簧-云测试-终结点禁用](#az-spring-cloud-test-endpoint-disable) | 禁用测试终结点。 |
| [az 春季-cloud test-endpoint enable](#az-spring-cloud-test-endpoint-enable) | 启用测试终结点。 |
| [az 春季-云测试-终结点列表](#az-spring-cloud-test-endpoint-list) | 列出测试终结点项。 |
| [az 春季-cloud 测试-endpoint 续订-密钥](#az-spring-cloud-test-endpoint-renew-key) | 重新生成测试终结点项。 |

## <a name="az-spring-cloud-create"></a>az 春季-cloud create

使用 Azure 春季云中的默认部署创建新应用。

```cli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| 必需的参数 | |
| --- | ---: |
| --name-n | 此 Azure 春季 Cloud 实例的名称。 |
| --resource-group -g | 指定此应用程序的资源组。  使用 `az configure --defaults group=<name>` 配置默认组 |

| 可选参数 | |
| --- | ---: |
| --location -l | 为此应用指定服务器位置。  使用 `az account list-locations` 查找有效位置 |
| --no-wait | 不要用于完成长时间运行的操作。

### <a name="examples"></a>示例

在 WestUS 中创建新的 Azure 春季云

```cli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>az 春季-cloud delete

删除 Azure 春季云实例。

```cli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| 必需的参数 | |
| --- | ---: |
| --name-n | 要删除的 Azure 春季云实例的名称。 |
| --resource-group -g | Azure 春季云中所属资源组的名称。 |

| 可选参数 | |
| --- | ---: |
| -否-等待 | 请勿等待长时间运行的操作完成。 |

### <a name="example"></a>示例

从 "MyResourceGroup" 中删除名为 "MyService" 的 Azure 春季云实例。

```cli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>az 春季-cloud list

列出与给定资源组关联的所有 Azure 春季云实例。 如果未指定资源组，则列出订阅 Id。

```cli
az spring-cloud list --resource-group -g
```

| 必需的参数 | |
| --- | ---: |
| --resource-group -g | 资源组的名称。 |

## <a name="az-spring-cloud-show"></a>az 春季-cloud show

显示指定的 Azure 春季云实例的详细信息。

```cli
az spring-cloud show --name -n
                     -- resource-group -g
```

| 必需的参数 | |
| --- | ---: |
| --name-n | Azure 春季 Cloud 实例的名称。 |
| --resource-group -g | Azure 春季 Cloud 实例所属的资源组的名称。

## <a name="az-spring-cloud-app-create"></a>az 春季-cloud app create

在 Azure 春季云中创建一个新的应用。

```cli
az spring-cloud app create --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --enable-persistent-storage
                            --instance-count
                            --is-public
                            --memory
```

| 必需的参数 | |
| --- | ---: |
| --name-n | 应用程序的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |

| 可选参数 | |
| --- | ---: |
| --cpu | 每个实例的虚拟核心数。  默认值：1。 |
| --enable-持久存储 | 布尔值。  如果为 true，则使用默认路径装载50GB 磁盘。 |
| --instance-count | 实例数。  默认值：1。 |
| --公有 | 布尔值。  如果为 true，则分配公共域。 |
| --内存 | 每个实例的内存 GB 数。  默认值：1。 |

### <a name="examples"></a>示例

使用默认配置创建应用。

```cli
az spring-cloud app create -n MyApp -s MyService
```

使用3个实例创建一个可公开访问的应用。  每个实例有 3 GB 内存和2个 CPU 内核。

```cli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>az 春季-cloud app delete

删除 Azure 春季云中的应用。

```cli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| 必需的参数 | |
| --- | ---: |
| --name-n | 应用程序的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |

## <a name="az-spring-cloud-app-deploy"></a>az 春季-cloud app deploy

通过源代码或预生成的二进制文件将应用部署到 Azure 春季 Cloud，并更新相关配置。

```cli
az spring cloud app deploy  --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --deployment -d
                            --env
                            --instance-count
                            --jar-path
                            --jvm-options
                            --memory
                            --no-wait
                            --runtime-version
                            --target-module
                            --version
```

| 必需的参数 | |
| --- | ---: |
| --name-n | 应用程序的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |

| 可选参数 | |
| --- | ---: |
| --cpu | 每个实例的虚拟 CPI 核心数。 |
| --部署-d | 现有应用部署的名称。  如果未指定，则默认为生产部署。 |
| --env | "键 [= 值]" 格式的以空格分隔的环境变量。 |
| --instance-count | 实例数。 |
| --jar-路径 | 如果提供，则从给定路径部署 jar。 否则，将当前文件夹部署为 tar。 |
| --jvm 选项 | 包含 JVM 选项的字符串。  使用 "=" 而不是 "" 来避免 shell 分析错误。 例如 `--jvm-options='-Xms1024m -Xmx2048m`。 |
| --内存 | 每个实例的内存 GB 数。 |
| --no-wait | 请勿等待长时间运行的操作完成。 |
| --运行时版本 | 应用中使用的语言的运行时版本。  允许的值： `Java_11`，`Java_8`。 |
| --target-module | 要部署的子模块。  在从源代码生成多个 jar 包时是必需的。 |
| --version | 部署版本。  如果未设置，则保持不变。 |

### <a name="examples"></a>示例

将源代码部署到应用。 这将打包当前目录，使用 Pivotal 生成服务生成一个二进制文件，然后将其部署到该应用。

```cli
az spring-cloud app deploy -n MyApp -s MyService
```

使用 JVM 选项和环境变量将预生成的 jar 部署到应用。

```cli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

将源代码部署到应用程序的特定部署。

```cli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>az 春季-cloud app list

列出 Azure 春季 Cloud 实例中的所有应用。

```cli
az spring-cloud app list --resource-group -g
                         --service -s
```

|必需的参数 | |
| --- | ---: |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |

## <a name="az-spring-cloud-app-restart"></a>az 春季-cloud app restart

重新启动应用的实例。  默认为生产部署。

```cli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| 必需的参数 | |
| --- | ---: |
| --name-n | 应用程序的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |

| 可选参数 | |
| --- | ---: |
| --部署-d | 应用的现有部署的名称。  如果未指定，则默认为生产部署。 |
| --no-wait | 请勿等待长时间运行的操作完成。 |

## <a name="az-spring-cloud-app-scale"></a>az 春季-cloud app scale

手动缩放应用或其部署。

```cli
az spring-cloud app scale --name -n
                          --resource-group -g
                          --service -s
                          --cpu
                          --deployment -d
                          --instance-count
                          --memory
                          --no-wait
```

| 必需的参数 | |
| --- | ---: |
| --name-n | 应用程序的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |

| 可选参数 | |
| --- | ---: |
| --cpu | 每个应用实例的虚拟 CPU 核心数。 |
| --部署-d | 应用的现有部署的名称。  如果未指定，则默认为生产部署。 |
| --instance-count | 此应用的实例数。 |
| --内存 | 每个应用实例的内存 GB 数。 |
| --no-wait | 请勿等待长时间运行的操作完成。 |

### <a name="examples"></a>示例

为每个实例将应用扩展到4个 CPU 核心和 8 GB 内存。

```cli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

向外扩展应用程序到5个实例的部署。

```cli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>az 弹簧-云应用集-部署

设置应用的生产部署的配置选项。

```cli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| 必需的参数 | |
| --- | ---: |
| --部署-d | 应用的现有部署的名称。 |
| --name-n | 应用程序的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |

| 可选参数 | |
| --- | ---: |
| --no-wait | 请勿等待长时间运行的操作完成。 |

### <a name="examples"></a>示例

将应用的过渡部署切换到生产环境。

```cli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>az 春季-cloud app show

在 Azure 春季云中显示应用的详细信息。

```cli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| 必需的参数 | |
| --- | ---: |
| --name-n | 应用程序的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |

## <a name="az-spring-cloud-app-show-deploy-log"></a>az 春季-cloud app show-deploy-log

显示源代码中最后一个部署的生成日志。  默认为生产环境。

```cli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| 必需的参数 | |
| --- | ---: |
| --name-n | 应用程序的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |

| 可选参数 | |
| --- | ---: |
| --部署-d | 应用的现有部署的名称。  默认为生产环境。 |

## <a name="az-spring-cloud-app-start"></a>az 春季-cloud app start

启动应用的实例。  默认为生产环境。

```cli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| 必需的参数 | |
| --- | ---: |
| --name-n | 应用程序的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |

| 可选参数 | |
| --- | ---: |
| --部署-d | 应用的现有部署的名称。  默认为生产环境。 |
| --no-wait | 请勿等待长时间运行的操作完成。 |

## <a name="az-spring-cloud-app-stop"></a>az 春季-cloud app stop

停止应用程序实例。  默认为生产环境。

```cli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| 必需的参数 | |
| --- | ---: |
| --name-n | 应用程序的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |

| 可选参数 | |
| --- | ---: |
| --部署-d | 应用的现有部署的名称。  默认为生产环境。 |
| --no-wait | 请勿等待长时间运行的操作完成。 |

## <a name="az-spring-cloud-app-update"></a>az 春季-cloud app update

更新应用程序的存储配置。

```cli
az spring-cloud app update --name -n
                           --resource-group -g
                           --service -s
                           --deployment -d
                           --enable-persistent-storage
                           --env
                           --is-public
                           --jvm-options
                           --no-wait
                           --runtime-version
```

| 必需的参数 | |
| --- | ---: |
| --name-n | 应用程序的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |

| 可选参数 | |
| --- | ---: |
| --部署-d | 应用的现有部署的名称。  默认为生产环境。 |
| --enable-持久存储 | 布尔值。  如果为 true，则使用默认路径装载50GB 磁盘。 |
| --env | "键 [= 值]" 格式的以空格分隔的环境变量。 |
| --公有 | 布尔值。  如果为 true，则向应用程序分配公共域。 |
| --jvm 选项 | 包含 JVM 选项的字符串。  使用 "=" 而不是 "" 来避免 shell 分析错误。 例如 `--jvm-options='-Xms1024m -Xmx2048m`。 |
| --no-wait | 请勿等待长时间运行的操作完成。 |
| --运行时版本 | 应用中使用的语言的运行时版本。  允许的值： `Java_11`，`Java_8`。 |

### <a name="example"></a>示例

添加应用的环境变量。

```cli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>az 弹簧-云应用绑定列表

列出应用中的所有服务绑定。

```cli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| 必需的参数 | |
| --- | ---: |
| --应用 | 应用程序的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |

## <a name="az-spring-cloud-app-binding-remove"></a>az 弹簧-云应用绑定删除

从应用中删除服务绑定。

```cli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| 必需的参数 | |
| --- | ---: |
| --应用 | 应用程序的名称。 |
| --name | 要删除的服务绑定的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |

## <a name="az-spring-cloud-app-binding-show"></a>az 春季-cloud app binding show

显示服务绑定的详细信息。

```cli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| 必需的参数 | |
| --- | ---: |
| --应用 | 应用程序的名称。 |
| --name | 服务绑定的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>az 春季-cloud app binding cosmos add

将 Azure Cosmos DB 与应用绑定。

```cli
az spring-cloud app binding cosmos add --api-type
                                       --app
                                       --name -n
                                       --resource-group -g
                                       --resource-id
                                       --service -s
                                       --collection-name
                                       --database-name
                                       --key-space
```

```cli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| 必需的参数 | |
| --- | ---: |
| --api 类型 | 使用以下值之一指定 API 类型： cassandra、gremlin、mongo、sql、table。 |
| --应用 | 应用程序的名称。 |
| --name | 服务绑定的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |

|可选参数 | |
| --- | ---: |
| --collection-name | 集合的名称。  当使用 Gremlin 时是必需的。 |
| --数据库名称 | 数据库的名称。  使用 Mongo、SQL 和 Gremlin 时是必需的。 |
| --key-space | Cassandra。  当使用 Cassandra 时是必需的。 |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>az 春季-cloud app binding cosmos update

```cli
az spring-cloud app binding cosmos update --app
                                          --name -n
                                          --resource-group -g
                                          --service -s
                                          --collection-name
                                          --database-name
                                          --key-space
```

| 必需的参数 | |
| --- | ---: |
| --应用 | 应用程序的名称。 |
| --name | 服务绑定的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |

|可选参数 | |
| --- | ---: |
| --collection-name | 集合的名称。  当使用 Gremlin 时是必需的。 |
| --数据库名称 | 数据库的名称。  使用 Mongo、SQL 和 Gremlin 时是必需的。 |
| --key-space | Cassandra。  当使用 Cassandra 时是必需的。 |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>az 春季-云应用绑定 mysql 添加

```cli
az spring-cloud app binding mysql add --app
                                      --database-name
                                      --key
                                      --name -n
                                      --resource-group -g
                                      --service -s
                                      --username
```

| 必需的参数 | |
| --- | ---: |
| --应用 | 应用程序的名称。 |
| --数据库名称 | 数据库的名称。 |
| --key | 服务的 API 密钥。 |
| --name | 服务绑定的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --资源 id | 要与之绑定的服务的 Azure 资源 ID。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |
| --username | 用于访问数据库的用户名。 |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>az 春季-cloud app binding mysql update

更新服务绑定，将应用连接到 Azure Database for MySQL。

```cli
az spring-cloud app binding mysql update --add
                                         --name -n
                                         --resource-group -g
                                         --service -s
                                         --database-name
                                         --key
                                         --username
```

| 必需的参数 | |
| --- | ---: |
| --应用 | 应用程序的名称。 |
| --name | 服务绑定的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |

| 可选参数 | |
| --- | ---: |
| --数据库名称 | 数据库的名称。 |
| --key | 服务的 API 密钥。 |
| --username | 用于访问数据库的用户名。 |

## <a name="az-spring-cloud-app-binding-redis-add"></a>az 春季-cloud app binding redis add

将用于 Redis 的 Azure 缓存与应用绑定。

```cli
az spring-cloud app binding redis add --app
                                      --name -n
                                      --resource-group -g
                                      --resource-id
                                      --service -s
                                      --disable-ssl
```

| 必需的参数 | |
| --- | ---: |
| --应用 | 应用程序的名称。 |
| --name | 服务绑定的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --资源 id | 要与之绑定的服务的 Azure 资源 ID。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |

| 可选参数 | |
| --- | ---: |
| --disable-ssl | 禁用 SSL。 |

## <a name="az-spring-cloud-app-binding-redis-update"></a>az 春季-cloud app binding redis update

为 Redis 更新 Azure 缓存的服务绑定。

| 必需的参数 | |
| --- | ---: |
| --应用 | 应用程序的名称。 |
| --name | 服务绑定的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |

| 可选参数 | |
| --- | ---: |
| --disable-ssl | 禁用 SSL。 |

## <a name="az-spring-cloud-app-deployment-create"></a>az 春季-cloud app deployment create

创建应用的过渡部署。

若要部署代码或将设置更新到现有部署，请使用 `az spring-cloud app deploy --deployment <staging-deployment>` 或 "az 春季-cloud app update--deployment <staging deployment>。

| 必需的参数 | |
| --- | ---: |
| --应用 | 应用程序的名称。 |
| --name | 服务绑定的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |

| 可选参数 | |
| --- | ---: |
| --cpu | 每个实例的虚拟 CPU 核心数。  默认值：1 |
| --env | "键 [= 值]" 格式的以空格分隔的环境变量。 |
| --instance-count | 实例数。 默认值：1。 |
| --jar-路径 | 如果已提供，请部署 jar。  否则，将当前文件夹部署为 tar。 |
| --jvm 选项 | 包含 JVM 选项的字符串。  使用 "=" 而不是 "" 来避免 shell 分析错误。 例如 `--jvm-options='-Xms1024m -Xmx2048m`。 |
| --内存 | 每个实例的内存 GB 数。 |
| --no-wait | 请勿等待长时间运行的操作完成。 |
| --运行时版本 | 应用中使用的语言的运行时版本。  允许的值： `Java_11`，`Java_8`。 |
| --skip-clone-设置 | 通过克隆当前生产部署设置来创建过渡部署。 |
| --target-module | 要部署的子模块。  在从源代码生成多个 jar 包时是必需的。 |
| --version | 部署版本。  如果未设置，则保持不变。 |

### <a name="examples"></a>示例

将源代码部署到应用程序的新部署。  这将打包当前目录，使用 Pivotal 生成系统生成一个二进制文件，然后部署。

```cli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

使用 JVM 选项和环境变量将预生成的 jar 部署到应用。

```cli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>az 春季-cloud app deployment delete

删除应用的部署。

```cli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| 必需的参数 | |
| --- | ---: |
| --应用 | 应用程序的名称。 |
| --name | 部署的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |

## <a name="az-spring-cloud-app-deployment-list"></a>az 春季-cloud app deployment list

列出应用中的所有部署。

```cli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| 必需的参数 | |
| --- | ---: |
| --应用 | 应用程序的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |

## <a name="az-spring-cloud-app-deployment-show"></a>az 春季-cloud app deployment show

显示部署的详细信息。

```cli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| 必需的参数 | |
| --- | ---: |
| --应用 | 应用程序的名称。 |
| --name | 部署的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --service-s | Azure 春季云的名称。  您可以使用 `az configure --defaults spring-cloud=<name>`配置默认服务。 |

## <a name="az-spring-cloud-config-server-clear"></a>az 春季-cloud config-server clear

清除配置服务器中的所有配置设置。

```cli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| 必需的参数 | |
| --- | ---: |
| --name | Azure 春季云的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |

## <a name="az-spring-cloud-config-server-set"></a>az 春季-云 config-服务器集

使用 YAML 文件设置配置服务器上的配置设置。

```cli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| 必需的参数 | |
| --- | ---: |
| --config-file | 配置服务器配置的 YAML 清单的文件路径。 |
| --name | Azure 春季云的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |

| 可选参数 | |
| --- | ---: |
| --no-wait | 不要用于完成长时间运行的操作。

## <a name="az-spring-cloud-config-server-show"></a>az 春季-cloud config-server show

显示配置服务器设置。

```cli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| 必需的参数 | |
| --- | ---: |
| --name | Azure 春季云的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |

## <a name="az-spring-cloud-config-server-git-set"></a>az 春季-cloud config-server git set

设置配置服务器的 git 属性。  这会覆盖所有现有的 git 属性。

```cli
az spring-cloud config-server git set --name -n
                                      --resource-group g
                                      --uri
                                      --defer
                                      --host-key
                                      --host-key-algorithm
                                      --label
                                      --password
                                      --private-key
                                      --search-paths
                                      --strict-host-key-checking
                                      --username
```

| 必需的参数 | |
| --- | ---: |
| --name | Azure 春季云的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --uri | 添加的配置的 URI。 |

| 可选参数 | |
| --- | ---: |
| --延迟 | 暂时将该对象存储在本地缓存中，而不是发送到 Azure。  使用 `az cache` 进行查看/清除。 |
| --host 键 | 添加的配置的主机密钥。 |
| --主机密钥算法 | 添加的配置的主机密钥算法。 |
| --label | 添加的配置的标签。 |
| --password | 添加的配置的密码。 |
| --私钥 | 添加的配置的私钥。 |
| --搜索-路径 | 添加的配置的搜索路径。 对于多个路径，请使用逗号分隔符。 |
| --严格的主机键检查 | 对所添加的配置启用严格的主机密钥检查。 |
| --username | 添加的配置的用户名。 |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>az 春季-cloud config-server git 存储库添加

```cli
az spring-cloud config-server git repo add --name -n
                                           --repo-name
                                           --resource-group -g
                                           --uri
                                           --defer
                                           --host-key
                                           --host-key-algorithm
                                           --label
                                           --password
                                           --pattern
                                           --private-key
                                           --search-paths
                                           --strict-host-key-checking
                                           --username
```

| 必需的参数 | |
| --- | ---: |
| --name | Azure 春季云的名称。 |
| --存储库-名称 | 存储库的 URI。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --uri | 添加的配置的 URI。 |

| 可选参数 | |
| --- | ---: |
| --延迟 | 暂时将该对象存储在本地缓存中，而不是发送到 Azure。  使用 `az cache` 进行查看/清除。 |
| --host 键 | 添加的配置的主机密钥。 |
| --主机密钥算法 | 添加的配置的主机密钥算法。 |
| --label | 添加的配置的标签。 |
| --password | 添加的配置的密码。 |
| --pattern | 存储库的模式。  对于多个路径，请使用逗号分隔符。|
| --私钥 | 添加的配置的私钥。 |
| --搜索-路径 | 添加的配置的搜索路径。 对于多个路径，请使用逗号分隔符。 |
| --严格的主机键检查 | 对所添加的配置启用严格的主机密钥检查。 |
| --username | 添加的配置的用户名。 |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>az 春季-cloud config-server git 存储库列表

列出在配置服务器中定义的所有 git 存储库

```cli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| 必需的参数 | |
| --- | ---: |
| --name | Azure 春季云的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |

| 可选参数 | |
| --- | ---: |
| --延迟 | 暂时将该对象存储在本地缓存中，而不是发送到 Azure。  使用 `az cache` 进行查看/清除。 |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>az 春季-cloud config-server git 存储库删除

从配置服务器中删除现有 git 存储库配置。

```cli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| 必需的参数 | |
| --- | ---: |
| --name | Azure 春季云的名称。 |
| --存储库-名称 | 存储库的 URI。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |

| 可选参数 | |
| --- | ---: |
| --延迟 | 暂时将该对象存储在本地缓存中，而不是发送到 Azure。  使用 `az cache` 进行查看/清除。 |

## <a name="az-spring-cloud-test-endpoint-disable"></a>az 弹簧-云测试-终结点禁用

禁用 Azure 春季云的测试终结点

```cli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| 必需的参数 | |
| --- | ---: |
| --name | Azure 春季云的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |

## <a name="az-spring-cloud-test-endpoint-enable"></a>az 春季-cloud test-endpoint enable

启用 Azure 春季云测试终结点。 

```cli 
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| 必需的参数 | |
| --- | ---: |
| --name | Azure 春季云的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |

## <a name="az-spring-cloud-test-endpoint-list"></a>az 春季-云测试-终结点列表 

列出 Azure 春季云和可用的测试终结点密钥。

```cli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| 必需的参数 | |
| --- | ---: |
| --name | Azure 春季云的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |

| 可选参数 | |
| --- | ---: |
| --应用 | 应用程序的名称。 |
| --部署-d | 应用的现有部署的名称。  如果未指定，则默认为生产。 |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>az 春季-cloud 测试-endpoint 续订-密钥

重新生成 Azure 春季云的测试终结点项。

```cli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| 必需的参数 | |
| --- | ---: |
| --name | Azure 春季云的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --type | 测试终结点键的类型。  允许的值： Primary、辅助。 |
