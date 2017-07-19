---
title: "使用 Azure CLI 2.0 管理 Service Fabric 应用程序"
description: "介绍了如何使用 Azure CLI 2.0 部署和删除 Service Fabric 群集中的应用程序"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: a99819f6a1c0ef31e14c95b6bd47138feb05053f
ms.contentlocale: zh-cn
ms.lasthandoff: 07/01/2017

---
# <a name="manage-service-fabric-application-using-azure-cli-20"></a>使用 Azure CLI 2.0 管理 Service Fabric 应用程序

若要创建和删除在 Service Fabric 群集中运行的应用程序，请按照本文档中的说明操作。

## <a name="prerequisites"></a>先决条件

请务必安装 Azure CLI 2.0，并选择 Service Fabric 群集。 有关详细信息，请参阅 [Azure CLI 2.0 入门文档](service-fabric-azure-cli-2-0.md)。

还应有可供部署的 Service Fabric 应用程序包。 若要详细了解如何创建并打包应用程序，请参阅[应用程序建模文档](service-fabric-application-model.md)。

## <a name="overview"></a>概述

部署新应用程序分为以下四步：

1. 将应用程序包上传到 Service Fabric 映像存储区
1. 预配应用程序类型
1. 指定并创建应用程序
1. 指定并创建服务

若要删除现有应用程序，需要执行以下三步：

1. 删除应用程序
1. 取消预配关联的应用程序类型
1. 删除映像存储区内容

## <a name="deploy-a-new-application"></a>部署新应用程序

若要部署新应用程序，按照以下步骤操作

### <a name="upload-a-new-application-package-to-the-image-store"></a>将新应用程序包上传到映像存储区

必须先将应用程序包上传到 Service Fabric 映像存储区，然后才能创建应用程序。
假设应用程序包位于 `app_package_dir` 目录中。 运行以下命令，上传此目录：

```azurecli
az sf application upload --path ~/app_package_dir
```

对于大型应用程序包，可以指定 `--show-progress` 选项来显示上传进度。

### <a name="provision-application-type"></a>预配应用程序类型

上传完成后，需要预配应用程序。 若要预配应用程序，请运行以下命令

```azurecli
az sf application provision --application-type-build-path app_package_dir
```

`application-type-build-path` 与包含之前上传的应用程序包的目录同名

### <a name="create-application-from-application-type"></a>根据应用程序类型创建应用程序

预配应用程序后，可以运行下列命令，命名并创建应用程序：

```azurecli
az sf application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

其中，`app-name` 是要为应用程序实例命名的名称。 有关其他参数，可以参阅之前预配的应用程序清单。

应用程序名称应以 `fabric:/` 前缀开头。

### <a name="create-services-for-the-new-application"></a>为新应用程序创建服务

创建应用程序后，可以通过应用程序创建服务。 在此示例中，我们要通过应用程序新建无状态服务。 在之前预配的应用程序包内的服务清单中，定义可以通过应用程序创建的服务。

```azurecli
az sf service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-creation-and-health"></a>验证应用程序创建和运行状况

若要验证是否已成功部署应用程序和服务，可以运行下列命令，检查应用程序和服务是否列出：

```azurecli
az sf application list
az sf service list --application-list TestApp
```

若要验证服务是否正常运行，请运行与以下类似的命令，检索服务和应用程序的运行状况

```azurecli
az sf application health --application-id TestApp
az sf service health --service-id TestApp/TestSvc
```

正常运行的服务和应用程序的 `Ok` 值应为 `HealthState`。

## <a name="remove-an-existing-application"></a>删除现有应用程序

若要删除应用程序，请按照以下步骤操作

### <a name="delete-the-application"></a>删除应用程序

运行以下命令，删除应用程序

```azurecli
az sf application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>取消预配应用程序类型

删除应用程序后，可以取消预配不再需要的应用程序类型。 运行以下命令，取消预配应用程序类型

```azurecli
az sf application unprovision --application-type-name TestAppTye --application-type-version 1.0
```

其中，类型名称和类型版本应与之前预配的应用程序清单中的名称和版本一致

### <a name="delete-application-package"></a>删除应用程序包

取消预配应用程序类型后，可以从映像存储区中删除不再需要的应用程序包。 删除应用程序包有助于回收磁盘空间。 运行以下命令，从映像存储区中删除应用程序包：

```azurecli
az sf application package-delete --content-path app_package_dir
```

其中，`content-path` 应与最初在创建应用程序时上传的目录同名

## <a name="related-articles"></a>相关文章

* [Service Fabric 和 Azure CLI 2.0 入门](service-fabric-azure-cli-2-0.md)
* [Service Fabric XPlat CLI 入门](service-fabric-azure-cli.md)

