---
title: "使用 Azure Service Fabric CLI 管理 Azure Service Fabric 应用程序"
description: "了解如何使用 Azure Service Fabric CLI 在 Azure Service Fabric 群集中部署和删除应用程序"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: article
ms.date: 08/22/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: c3a2eb3e6e54f952ef963bb2a0292d9ad7b53bc5
ms.contentlocale: zh-cn
ms.lasthandoff: 08/24/2017

---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli"></a>使用 Azure Service Fabric CLI 管理 Azure Service Fabric 应用程序

了解如何创建和删除在 Azure Service Fabric 群集中运行的应用程序。

## <a name="prerequisites"></a>先决条件

* 安装 Service Fabric CLI。 然后选择 Service Fabric 群集。 有关详细信息，请参阅 [Service Fabric CLI 入门](service-fabric-cli.md)。

* 已准备好随时可供部署的 Service Fabric 应用程序包。 有关如何创建和打包应用程序的详细信息，请参阅 [Service Fabric 应用程序模型](service-fabric-application-model.md)。

## <a name="overview"></a>概述

若要部署新应用程序，请完成以下步骤：

1. 将应用程序包上传到 Service Fabric 映像存储中。
2. 预配应用程序类型。
3. 指定并创建应用程序。
4. 指定并创建服务。

若要删除现有应用程序，请完成以下步骤：

1. 删除应用程序。
2. 取消预配关联的应用程序类型。
3. 删除映像存储内容。

## <a name="deploy-a-new-application"></a>部署新应用程序

要部署新应用程序，请完成以下任务：

### <a name="upload-a-new-application-package-to-the-image-store"></a>将新应用程序包上传到映像存储区

在创建应用程序之前，先将应用程序包上传到 Service Fabric 映像存储。

例如，如果应用程序包在 `app_package_dir` 目录中，请使用以下命令上传该目录：

```azurecli
sfctl application upload --path ~/app_package_dir
```

对于大型应用程序包，可以指定 `--show-progress` 选项来显示上传进度。

### <a name="provision-the-application-type"></a>预配应用程序类型

完成上传后，预配应用程序。 若要预配应用程序，请运行以下命令：

```azurecli
sfctl application provision --application-type-build-path app_package_dir
```

`application-type-build-path` 的值是将应用程序包上传到的目录的名称。

### <a name="create-an-application-from-an-application-type"></a>基于应用程序类型创建应用程序

预配应用程序后，使用以下命令来命名并创建应用程序：

```azurecli
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` 是要用于应用程序实例的名称。 可以从前面预配的应用程序清单获取其他参数。

应用程序名称必须以 `fabric:/` 前缀开头。

### <a name="create-services-for-the-new-application"></a>为新应用程序创建服务

创建应用程序后，通过该应用程序创建服务。 在以下示例中，我们通过自己的应用程序创建新的无状态服务。 在前面预配的应用程序包中的服务清单内，定义可通过应用程序创建的服务。

```azurecli
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>验证应用程序部署和运行状况

要验证一切运行正常，请使用以下运行状况命令：

```azurecli
sfctl application list
sfctl service list --application-id TestApp
```

若要验证服务是否正常运行，请使用类似的命令检索服务和应用程序的运行状况：

```azurecli
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

正常的服务和应用程序的 `HealthState` 值为 `Ok`。

## <a name="remove-an-existing-application"></a>删除现有应用程序

要删除应用程序，请完成以下任务：

### <a name="delete-the-application"></a>删除应用程序

若要删除应用程序，请使用以下命令：

```azurecli
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>取消预配应用程序类型

删除应用程序后，如果不再需要它，可以取消预配应用程序类型。 若要取消预配应用程序类型，请使用以下命令：

```azurecli
sfctl application unprovision --application-type-name TestAppTye --application-type-version 1.0
```

类型名称和类型版本必须与前面预配的应用程序清单中的名称和版本匹配。

### <a name="delete-the-application-package"></a>删除应用程序包

取消预配应用程序类型后，如果不再需要应用程序包，可从映像存储中删除它。 删除应用程序包有助于回收磁盘空间。 

若要从映像存储中删除应用程序包，请使用以下命令：

```azurecli
sfctl store delete --content-path app_package_dir
```

`content-path` 必须是创建应用程序时上传的目录的名称。

## <a name="upgrade-application"></a>升级应用程序

创建应用程序后，可重复一组相同的步骤来预配应用程序的第二个版本。 然后，可通过 Service Fabric 应用程序升级转换为运行第二个版本的应用程序。 有关详细信息，请参阅有关 [Service Fabric 应用程序升级](service-fabric-application-upgrade.md)的文档。

要执行升级，请首先使用与之前相同的命令预配应用程序的下一个版本：

```azurecli
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
```

建议随后执行受监视的自动升级，通过运行以下命令启动升级：

```azurecli
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

升级可通过指定的任何设置覆盖现有参数。 如有必要，应用程序参数应作为参数传递到升级命令。 应用程序参数应编码为 JSON 对象。

要检索以前指定的任何参数，可使用 `sfctl application info` 命令。

在应用程序升级过程时，可使用 `sfctl application upgrade-status` 命令检索状态。

最后，如果正在进行升级但需要取消此次升级，可使用 `sfctl application upgrade-rollback` 回滚升级。

## <a name="next-steps"></a>后续步骤

* [Service Fabric CLI 基础知识](service-fabric-cli.md)
* [Linux 上的 Service Fabric 入门](service-fabric-get-started-linux.md)
* [启动 Service Fabric 应用程序升级](service-fabric-application-upgrade.md)

