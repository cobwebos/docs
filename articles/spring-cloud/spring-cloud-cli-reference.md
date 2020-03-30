---
title: 阿兹春云
description: 使用 Azure CLI 管理 Azure 春云
author: bmitchell287
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: brendm
ms.openlocfilehash: 33d13d2d4fa9003ef041c4c96be83a69ac595a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298774"
---
# <a name="az-spring-cloud"></a>阿兹春云

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>使用 Azure CLI 管理 Azure 春云

>[!Note]
> Azure Spring Cloud 目前为预览版。  这些命令可能会在将来的版本中更改或删除。

| 阿兹春云 |  |
|------|------:|
| [az 弹簧云创建](#az-spring-cloud-create) | 创建 Azure 春云实例。 |
| [az 弹簧云删除](#az-spring-cloud-delete) | 删除 Azure 春云实例。 |
| [az 弹簧云列表](#az-spring-cloud-list) | 列出给定资源组中的所有 Azure 春云实例，否则列出订阅标识。 |
| [阿兹春云秀](#az-spring-cloud-show) | 显示 Azure 春云的详细信息。 |

| az 春云应用程序 | 管理 Azure 春云中的应用的命令。  |
| ---- | ----: |
| [az 弹簧云应用程序创建](#az-spring-cloud-app-create) | 在 Azure 春云中创建具有默认部署的新应用。 |
| [az 弹簧云应用程序删除](#az-spring-cloud-app-delete) | 删除 Azure 春云中的应用。 |
| [az 弹簧云应用部署](#az-spring-cloud-app-deploy) | 从源代码或预构建的二进制文件部署到应用并更新相关配置。 |
| [az 弹簧云应用列表](#az-spring-cloud-app-list) | 列出 Azure 春云中的所有应用。 |
| [az 弹簧云应用程序重新启动](#az-spring-cloud-app-restart) | 使用生产部署默认值重新启动应用实例。 |
| [az 春云应用规模](#az-spring-cloud-app-scale) | 手动缩放应用或其部署。 |
| [az 弹簧云应用集部署](#az-spring-cloud-app-set-deployment) | 设置应用的生产部署。 |
| [az 春云应用程序显示](#az-spring-cloud-app-show) | 在 Azure 春云中显示应用的详细信息。 |
| [az 弹簧云应用程序显示部署日志](#az-spring-cloud-app-show-deploy-log) | 从源显示最新部署的生成日志。 默认为生产部署。 |
| [az 弹簧云应用程序启动](#az-spring-cloud-app-start) | 使用生产部署默认值启动应用实例。 |
| [az 弹簧云应用程序停止](#az-spring-cloud-app-stop) | 使用生产部署默认值停止应用实例。 |
| [az 弹簧云应用程序更新](#az-spring-cloud-app-update) | 更新指定的应用的配置。 |

| az 弹簧云应用程序绑定 | 使用 Azure 数据服务管理绑定的命令。  在这些设置生效之前，必须重新启动应用。 |
| --- | ---: |
| [az 弹簧云应用绑定列表](#az-spring-cloud-app-binding-list) | 列出应用中的所有服务绑定。 |
| [az 弹簧云应用绑定删除](#az-spring-cloud-app-binding-remove) | 从应用中删除服务绑定。 |
| [az 弹簧云应用程序绑定显示](#az-spring-cloud-app-binding-show) | 显示服务绑定的详细信息。 |
| [az 弹簧云应用程序绑定宇宙添加](#az-spring-cloud-app-binding-cosmos-add) | 将 Azure CosmosDB 与应用绑定。 |
| [az 弹簧云应用程序绑定宇宙更新](#az-spring-cloud-app-binding-cosmos-update) | 更新 Azure CosmosDB 服务绑定。 |
| [az 弹簧云应用程序绑定 mysql 添加](#az-spring-cloud-app-binding-mysql-add) | 将 MySQL 的 Azure 数据库与应用绑定。 |
| [az 弹簧云应用程序绑定 mysql 更新](#az-spring-cloud-app-binding-mysql-update) | 更新 MySQL 服务绑定的 Azure 数据库。 |
| [az 弹簧云应用程序绑定 redis 添加](#az-spring-cloud-app-binding-redis-add) | 将 Redis 的 Azure 缓存与应用绑定。 |
| [az 弹簧云应用程序绑定 redis 更新](#az-spring-cloud-app-binding-redis-update) | 更新 Redis 服务绑定的 Azure 缓存。 |

| az 弹簧云应用部署 | 用于在 Azure Spring Cloud 中管理应用的部署生命周期的命令。 |
| --- | ---: |
| [az 弹簧云应用部署创建](#az-spring-cloud-app-deployment-create) | 为应用创建暂存部署。 |
| [az 弹簧云应用部署删除](#az-spring-cloud-app-deployment-delete) | 删除应用的部署。 |
| [az 弹簧云应用部署列表](#az-spring-cloud-app-deployment-list) | 列出应用中的所有部署。 |
| [az 弹簧云应用部署显示](#az-spring-cloud-app-deployment-show) | 显示部署的详细信息。 |

| az 弹簧云配置服务器 | 管理 Azure 春云配置服务器的命令。 |
| --- | ---: |
| [az 弹簧云配置服务器清除](#az-spring-cloud-config-server-clear) | 擦除配置服务器中的所有设置。 |
| [az 弹簧云配置服务器集](#az-spring-cloud-config-server-set) | 从 YAML 文件定义配置服务器。 |
| [az 弹簧云配置服务器显示](#az-spring-cloud-config-server-show) | 显示配置服务器配置。 |
| [az 弹簧云配置服务器 git 集](#az-spring-cloud-config-server-git-set) | 为配置服务器定义 git 属性。  以前的值将被覆盖。 |
| [az 弹簧云配置服务器 git 存储库 添加](#az-spring-cloud-config-server-git-repo-add) | 向配置服务器添加新 git 存储库配置。 |
| [az 弹簧云配置服务器 git 存储库列表](#az-spring-cloud-config-server-git-repo-list) | 列出配置服务器的所有 git 存储库配置。 |
| [az 弹簧云配置服务器 git 存储库删除](#az-spring-cloud-config-server-git-repo-remove) | 从配置服务器中删除指定的 git 存储库。 |

| az 弹簧云测试端点 | 在 Azure 春云中管理终结点测试的命令 |
| --- | ---: |
| [az 弹簧云测试终结点禁用](#az-spring-cloud-test-endpoint-disable) | 禁用测试终结点。 |
| [az 弹簧云测试终结点启用](#az-spring-cloud-test-endpoint-enable) | 启用测试终结点。 |
| [az 弹簧云测试终结点列表](#az-spring-cloud-test-endpoint-list) | 列出测试终结点键。 |
| [az 弹簧云测试端点续订键](#az-spring-cloud-test-endpoint-renew-key) | 重新生成测试终结点密钥。 |

## <a name="az-spring-cloud-create"></a>az 弹簧云创建

在 Azure 春云中创建具有默认部署的新应用。

```azurecli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| 必需的参数 | |
| --- | ---: |
| --名称 -n | 此 Azure 春云实例的名称。 |
| --resource-group -g | 指定此应用的资源组。  使用配置默认组`az configure --defaults group=<name>` |

| 可选参数 | |
| --- | ---: |
| --location -l | 指定此应用程序的服务器位置。  使用`az account list-locations` |
| --no-wait | 不要为长时间运行的操作完成。

### <a name="examples"></a>示例

在西部创建新的 Azure 春云

```azurecli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>az 弹簧云删除

删除 Azure 春云实例。

```azurecli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| 必需的参数 | |
| --- | ---: |
| --名称 -n | 要删除的 Azure 春云实例的名称。 |
| --resource-group -g | Azure 春云所属的资源组的名称。 |

| 可选参数 | |
| --- | ---: |
| -不等待 | 不要等待长时间运行的操作完成。 |

### <a name="example"></a>示例

从"我的资源组"中删除名为"我的服务"的 Azure 春云实例。

```azurecli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>az 弹簧云列表

列出与给定资源组关联的所有 Azure 春云实例。 如果未指定资源组，则列出订阅代码。

```azurecli
az spring-cloud list --resource-group -g
```

| 必需的参数 | |
| --- | ---: |
| --resource-group -g | 资源组的名称。 |

## <a name="az-spring-cloud-show"></a>阿兹春云秀

显示指定的 Azure 春云实例的详细信息。

```azurecli
az spring-cloud show --name -n
                     -- resource-group -g
```

| 必需的参数 | |
| --- | ---: |
| --名称 -n | Azure 春云实例的名称。 |
| --resource-group -g | Azure 春云实例所属的资源组的名称。

## <a name="az-spring-cloud-app-create"></a>az 弹簧云应用程序创建

在 Azure 春云中创建新应用。

```azurecli
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
| --名称 -n | 应用的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |

| 可选参数 | |
| --- | ---: |
| --cpu | 每个实例的虚拟内核数。  默认值：1。 |
| --启用持久存储 | 布尔值。  如果为 true，则装载具有默认路径的 50GB 磁盘。 |
| --instance-count | 实例数。  默认值：1。 |
| --是公共的 | 布尔值。  如果为 true，则分配公有域。 |
| --内存 | 每个实例的 GB 内存数。  默认值：1。 |

### <a name="examples"></a>示例

创建具有默认配置的应用。

```azurecli
az spring-cloud app create -n MyApp -s MyService
```

创建具有 3 个实例的可公开访问的应用。  每个实例都有 3 GB 的内存和 2 个 CPU 内核。

```azurecli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>az 弹簧云应用程序删除

删除 Azure 春云中的应用。

```azurecli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| 必需的参数 | |
| --- | ---: |
| --名称 -n | 应用的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |

## <a name="az-spring-cloud-app-deploy"></a>az 弹簧云应用部署

从源代码或预构建的二进制文件将应用部署到 Azure 春云，并更新相关配置。

```azurecli
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
| --名称 -n | 应用的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |

| 可选参数 | |
| --- | ---: |
| --cpu | 每个实例的虚拟 CPI 核心数。 |
| --部署-d | 现有应用部署的名称。  如果未指定，则默认为生产部署。 |
| --恩夫 | 空间分隔环境变量采用"键[值]"格式。 |
| --instance-count | 实例数。 |
| --jar-path | 如果提供，则从给定路径部署 jar。 否则，将当前文件夹部署为 tar。 |
| --jvm 选项 | 包含 JVM 选项的字符串。  使用"*"而不是''来避免 shell 分析错误。 例如， `--jvm-options='-Xms1024m -Xmx2048m`. |
| --内存 | 每个实例的 GB 内存数。 |
| --no-wait | 不要等待长时间运行的操作完成。 |
| --运行时版本 | 应用中使用的语言的运行时版本。  允许的值： `Java_11` `Java_8`. |
| --目标模块 | 要部署的子模块。  从源代码生成多个 jar 包时是必需的。 |
| --version | 部署版本。  如果未设置，则保持不变。 |

### <a name="examples"></a>示例

将源代码部署到应用。 这将打包当前目录，使用 Pivotal 生成服务构建二进制文件，然后部署到应用。

```azurecli
az spring-cloud app deploy -n MyApp -s MyService
```

使用 JVM 选项和环境变量将预构建的 jar 部署到应用。

```azurecli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

将源代码部署到应用的特定部署。

```azurecli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>az 弹簧云应用列表

列出 Azure 春云实例中的所有应用。

```azurecli
az spring-cloud app list --resource-group -g
                         --service -s
```

|必需的参数 | |
| --- | ---: |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |

## <a name="az-spring-cloud-app-restart"></a>az 弹簧云应用程序重新启动

重新启动应用的实例。  默认为生产部署。

```azurecli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| 必需的参数 | |
| --- | ---: |
| --名称 -n | 应用的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |

| 可选参数 | |
| --- | ---: |
| --部署-d | 应用的现有部署的名称。  如果未指定，则默认为生产部署。 |
| --no-wait | 不要等待长时间运行的操作完成。 |

## <a name="az-spring-cloud-app-scale"></a>az 春云应用规模

手动缩放应用或其部署。

```azurecli
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
| --名称 -n | 应用的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |

| 可选参数 | |
| --- | ---: |
| --cpu | 每个应用实例的虚拟 CPU 内核数。 |
| --部署-d | 应用的现有部署的名称。  如果未指定，则默认为生产部署。 |
| --instance-count | 此应用程序的实例数。 |
| --内存 | 每个应用实例的 GB 内存数。 |
| --no-wait | 不要等待长时间运行的操作完成。 |

### <a name="examples"></a>示例

将应用扩展为 4 个 CPU 内核和每个实例的 8 GB 内存。

```azurecli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

将应用的部署扩展到 5 个实例。

```azurecli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>az 弹簧云应用集部署

设置应用的生产部署的配置选项。

```azurecli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| 必需的参数 | |
| --- | ---: |
| --部署-d | 应用的现有部署的名称。 |
| --名称 -n | 应用的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |

| 可选参数 | |
| --- | ---: |
| --no-wait | 不要等待长时间运行的操作完成。 |

### <a name="examples"></a>示例

将应用的暂存部署交换到生产。

```azurecli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>az 春云应用程序显示

在 Azure 春云中显示应用的详细信息。

```azurecli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| 必需的参数 | |
| --- | ---: |
| --名称 -n | 应用的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |

## <a name="az-spring-cloud-app-show-deploy-log"></a>az 弹簧云应用程序显示部署日志

从源代码显示上次部署的生成日志。  默认为生产环境。

```azurecli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| 必需的参数 | |
| --- | ---: |
| --名称 -n | 应用的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |

| 可选参数 | |
| --- | ---: |
| --部署-d | 应用的现有部署的名称。  默认为生产环境。 |

## <a name="az-spring-cloud-app-start"></a>az 弹簧云应用程序启动

启动应用实例。  默认为生产环境。

```azurecli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| 必需的参数 | |
| --- | ---: |
| --名称 -n | 应用的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |

| 可选参数 | |
| --- | ---: |
| --部署-d | 应用的现有部署的名称。  默认为生产环境。 |
| --no-wait | 不要等待长时间运行的操作完成。 |

## <a name="az-spring-cloud-app-stop"></a>az 弹簧云应用程序停止

停止应用的实例。  默认为生产环境。

```azurecli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| 必需的参数 | |
| --- | ---: |
| --名称 -n | 应用的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |

| 可选参数 | |
| --- | ---: |
| --部署-d | 应用的现有部署的名称。  默认为生产环境。 |
| --no-wait | 不要等待长时间运行的操作完成。 |

## <a name="az-spring-cloud-app-update"></a>az 弹簧云应用程序更新

更新应用程序的存储配置。

```azurecli
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
| --名称 -n | 应用的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |

| 可选参数 | |
| --- | ---: |
| --部署-d | 应用的现有部署的名称。  默认为生产环境。 |
| --启用持久存储 | 布尔值。  如果为 true，则使用默认路径装载 50GB 磁盘。 |
| --恩夫 | 空间分隔环境变量采用"键[值]"格式。 |
| --是公共的 | 布尔值。  如果为 true，则为应用分配公有域。 |
| --jvm 选项 | 包含 JVM 选项的字符串。  使用"*"而不是''来避免 shell 分析错误。 例如， `--jvm-options='-Xms1024m -Xmx2048m`. |
| --no-wait | 不要等待长时间运行的操作完成。 |
| --运行时版本 | 应用中使用的语言的运行时版本。  允许的值： `Java_11` `Java_8`. |

### <a name="example"></a>示例

为应用添加环境变量。

```azurecli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>az 弹簧云应用绑定列表

列出应用中的所有服务绑定。

```azurecli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| 必需的参数 | |
| --- | ---: |
| --应用 | 应用的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |

## <a name="az-spring-cloud-app-binding-remove"></a>az 弹簧云应用绑定删除

从应用中删除服务绑定。

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| 必需的参数 | |
| --- | ---: |
| --应用 | 应用的名称。 |
| --name | 要删除的服务绑定的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |

## <a name="az-spring-cloud-app-binding-show"></a>az 弹簧云应用程序绑定显示

显示服务绑定的详细信息。

```azurecli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| 必需的参数 | |
| --- | ---: |
| --应用 | 应用的名称。 |
| --name | 服务绑定的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>az 弹簧云应用程序绑定宇宙添加

将 Azure 宇宙 DB 与应用绑定。

```azurecli
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

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| 必需的参数 | |
| --- | ---: |
| --api 类型 | 使用以下值之一指定 API 类型：卡桑德拉、格林林、蒙戈、sql、表。 |
| --应用 | 应用的名称。 |
| --name | 服务绑定的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |

|可选参数 | |
| --- | ---: |
| --集合名称 | 集合的名称。  使用格雷姆林时需要。 |
| --数据库名称 | 数据库的名称。  使用蒙戈、SQL 和 Gremlin 时是必需的。 |
| --键空间 | 卡桑德拉键空间。  使用卡桑德拉时需要。 |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>az 弹簧云应用程序绑定宇宙更新

```azurecli
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
| --应用 | 应用的名称。 |
| --name | 服务绑定的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |

|可选参数 | |
| --- | ---: |
| --集合名称 | 集合的名称。  使用格雷姆林时需要。 |
| --数据库名称 | 数据库的名称。  使用蒙戈、SQL 和 Gremlin 时是必需的。 |
| --键空间 | 卡桑德拉键空间。  使用卡桑德拉时需要。 |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>az 弹簧云应用程序绑定 mysql 添加

```azurecli
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
| --应用 | 应用的名称。 |
| --数据库名称 | 数据库的名称。 |
| --key | 服务的 API 密钥。 |
| --name | 服务绑定的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --资源 ID | 要绑定的服务的 Azure 资源 ID。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |
| --username | 数据库访问的用户名。 |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>az 弹簧云应用程序绑定 mysql 更新

更新应用程序与 MySQL Azure 数据库的服务绑定连接。

```azurecli
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
| --应用 | 应用的名称。 |
| --name | 服务绑定的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |

| 可选参数 | |
| --- | ---: |
| --数据库名称 | 数据库的名称。 |
| --key | 服务的 API 密钥。 |
| --username | 数据库访问的用户名。 |

## <a name="az-spring-cloud-app-binding-redis-add"></a>az 弹簧云应用程序绑定 redis 添加

将 Redis 的 Azure 缓存与应用绑定。

```azurecli
az spring-cloud app binding redis add --app
                                      --name -n
                                      --resource-group -g
                                      --resource-id
                                      --service -s
                                      --disable-ssl
```

| 必需的参数 | |
| --- | ---: |
| --应用 | 应用的名称。 |
| --name | 服务绑定的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --资源 ID | 要绑定的服务的 Azure 资源 ID。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |

| 可选参数 | |
| --- | ---: |
| --禁用-ssl | 禁用 TLS。 |

## <a name="az-spring-cloud-app-binding-redis-update"></a>az 弹簧云应用程序绑定 redis 更新

更新 Redis 的 Azure 缓存的服务绑定。

| 必需的参数 | |
| --- | ---: |
| --应用 | 应用的名称。 |
| --name | 服务绑定的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |

| 可选参数 | |
| --- | ---: |
| --禁用-ssl | 禁用 TLS。 |

## <a name="az-spring-cloud-app-deployment-create"></a>az 弹簧云应用部署创建

为应用创建暂存部署。

要部署代码或将设置更新到现有部署，请使用`az spring-cloud app deploy --deployment <staging-deployment>`或 "az 弹簧云应用更新<staging deployment>- 部署"。

| 必需的参数 | |
| --- | ---: |
| --应用 | 应用的名称。 |
| --name | 服务绑定的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |

| 可选参数 | |
| --- | ---: |
| --cpu | 每个实例的虚拟 CPU 内核数。  默认值：1 |
| --恩夫 | 空间分隔环境变量采用"键[值]"格式。 |
| --instance-count | 实例数。 默认值：1。 |
| --jar-path | 如果提供，则部署 jar。  否则，将当前文件夹部署为 tar。 |
| --jvm 选项 | 包含 JVM 选项的字符串。  使用"*"而不是''来避免 shell 分析错误。 例如， `--jvm-options='-Xms1024m -Xmx2048m`. |
| --内存 | 每个实例的 GB 内存数。 |
| --no-wait | 不要等待长时间运行的操作完成。 |
| --运行时版本 | 应用中使用的语言的运行时版本。  允许的值： `Java_11` `Java_8`. |
| --跳过克隆设置 | 通过克隆当前生产部署设置创建暂存部署。 |
| --目标模块 | 要部署的子模块。  从源代码生成多个 jar 包时是必需的。 |
| --version | 部署版本。  如果未设置，则保持不变。 |

### <a name="examples"></a>示例

将源代码部署到应用的新部署。  这将打包当前目录，使用 Pivotal Build 系统构建二进制文件，然后进行部署。

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

将预构建的 jar 部署到具有 JVM 选项和环境变量的应用。

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>az 弹簧云应用部署删除

删除应用的部署。

```azurecli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| 必需的参数 | |
| --- | ---: |
| --应用 | 应用的名称。 |
| --name | 部署的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |

## <a name="az-spring-cloud-app-deployment-list"></a>az 弹簧云应用部署列表

列出应用中的所有部署。

```azurecli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| 必需的参数 | |
| --- | ---: |
| --应用 | 应用的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |

## <a name="az-spring-cloud-app-deployment-show"></a>az 弹簧云应用部署显示

显示部署的详细信息。

```azurecli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| 必需的参数 | |
| --- | ---: |
| --应用 | 应用的名称。 |
| --name | 部署的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --服务 -s | Azure 春云的名称。  您可以使用 配置默认服务`az configure --defaults spring-cloud=<name>`。 |

## <a name="az-spring-cloud-config-server-clear"></a>az 弹簧云配置服务器清除

擦除配置服务器中的所有配置设置。

```azurecli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| 必需的参数 | |
| --- | ---: |
| --name | Azure 春云的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |

## <a name="az-spring-cloud-config-server-set"></a>az 弹簧云配置服务器集

使用 YAML 文件在 Config 服务器上设置配置设置。

```azurecli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| 必需的参数 | |
| --- | ---: |
| --配置文件 | 用于配置配置配置的 YAML 清单的文件路径。 |
| --name | Azure 春云的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |

| 可选参数 | |
| --- | ---: |
| --no-wait | 不要为长时间运行的操作完成。

## <a name="az-spring-cloud-config-server-show"></a>az 弹簧云配置服务器显示

显示配置服务器设置。

```azurecli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| 必需的参数 | |
| --- | ---: |
| --name | Azure 春云的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |

## <a name="az-spring-cloud-config-server-git-set"></a>az 弹簧云配置服务器 git 集

设置配置服务器的 git 属性。  这将覆盖所有现有的 git 属性。

```azurecli
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
| --name | Azure 春云的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --尤里 | 添加配置的 URI。 |

| 可选参数 | |
| --- | ---: |
| --延迟 | 将对象暂时存储在本地缓存中，而不是发送到 Azure。  用于`az cache`查看/清除。 |
| --主机键 | 添加配置的主机密钥。 |
| --主机键算法 | 附加配置的主机密钥算法。 |
| --标签 | 添加配置的标签。 |
| --password | 添加配置的密码。 |
| --私钥 | 添加配置的私钥。 |
| --搜索路径 | 添加配置的搜索路径。 对多个路径使用逗号分隔符。 |
| --严格主机密钥检查 | 启用对添加的配置进行严格的主机密钥检查。 |
| --username | 添加配置的用户名。 |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>az 弹簧云配置服务器 git 存储库添加

```azurecli
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
| --name | Azure 春云的名称。 |
| --回购名称 | 回购的 URI。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --尤里 | 添加配置的 URI。 |

| 可选参数 | |
| --- | ---: |
| --延迟 | 将对象暂时存储在本地缓存中，而不是发送到 Azure。  用于`az cache`查看/清除。 |
| --主机键 | 添加配置的主机密钥。 |
| --主机键算法 | 附加配置的主机密钥算法。 |
| --标签 | 添加配置的标签。 |
| --password | 添加配置的密码。 |
| --模式 | 回购的模式。  对多个路径使用逗号分隔符。|
| --私钥 | 添加配置的私钥。 |
| --搜索路径 | 添加配置的搜索路径。 对多个路径使用逗号分隔符。 |
| --严格主机密钥检查 | 启用对添加的配置进行严格的主机密钥检查。 |
| --username | 添加配置的用户名。 |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>az 弹簧云配置服务器 git 存储库列表

列出配置服务器中定义的所有 git 存储库

```azurecli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| 必需的参数 | |
| --- | ---: |
| --name | Azure 春云的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |

| 可选参数 | |
| --- | ---: |
| --延迟 | 将对象暂时存储在本地缓存中，而不是发送到 Azure。  用于`az cache`查看/清除。 |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>az 弹簧云配置服务器 git 存储库删除

从配置服务器中删除现有的 git 存储库配置。

```azurecli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| 必需的参数 | |
| --- | ---: |
| --name | Azure 春云的名称。 |
| --回购名称 | 回购的 URI。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |

| 可选参数 | |
| --- | ---: |
| --延迟 | 将对象暂时存储在本地缓存中，而不是发送到 Azure。  用于`az cache`查看/清除。 |

## <a name="az-spring-cloud-test-endpoint-disable"></a>az 弹簧云测试终结点禁用

禁用 Azure 春云的测试终结点

```azurecli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| 必需的参数 | |
| --- | ---: |
| --name | Azure 春云的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |

## <a name="az-spring-cloud-test-endpoint-enable"></a>az 弹簧云测试终结点启用

为 Azure 春云启用测试终结点。 

```azurecli
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| 必需的参数 | |
| --- | ---: |
| --name | Azure 春云的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |

## <a name="az-spring-cloud-test-endpoint-list"></a>az 弹簧云测试终结点列表 

列出 Azure 春云的可用测试终结点键。

```azurecli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| 必需的参数 | |
| --- | ---: |
| --name | Azure 春云的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |

| 可选参数 | |
| --- | ---: |
| --应用 | 应用的名称。 |
| --部署-d | 应用的现有部署的名称。  如果未指定，则默认为生产。 |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>az 弹簧云测试端点续订键

重新生成 Azure 春云的测试终结点密钥。

```azurecli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| 必需的参数 | |
| --- | ---: |
| --name | Azure 春云的名称。 |
| --resource-group -g | 资源组的名称。  可以使用 `az configure --defaults group=<name>` 配置默认组。 |
| --类型 | 测试终结点密钥的类型。  允许的值：主值、辅助值。 |
