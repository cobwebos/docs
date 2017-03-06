---
title: "适用于 Azure Web 应用的基于 Azure Resource Manager 的跨平台命令行工具 | Microsoft Docs"
description: "了解如何使用新的基于 Azure Resource Manager 的跨平台命令行工具来管理 Azure Web 应用。"
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: d415b195-4262-416f-b59f-7e1aef200054
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 5ea7095e12b6194556d3cd0baa43ccfed1e087ee
ms.openlocfilehash: 7a03e1417617453c43edcc3787da10d171359757
ms.lasthandoff: 02/27/2017


---
# <a name="using-azure-resource-manager-based-xplat-cli-for-azure-app-service"></a>使用用于 Azure App Service 的基于 Azure Resource Manager 的 XPlat CLI
> [!div class="op_single_selector"]
> * [Azure CLI](app-service-web-app-azure-resource-manager-xplat-cli.md)
> * [Azure PowerShell](app-service-web-app-azure-resource-manager-powershell.md)

Microsoft Azure 跨平台命令行工具 0.10.5 版在发布时增加了新的命令。 这些命令使用户能够使用基于 Azure Resource Manager 的 PowerShell 命令来管理应用服务。

若要了解如何管理资源组，请参阅 [Use the Azure CLI to manage Azure resources and resource groups](../azure-resource-manager/xplat-cli-azure-resource-manager.md)（使用 Azure CLI 管理 Azure 资源和资源组）。 

> [!NOTE] 
> 还可试用 [Azure CLI 2.0](https://github.com/Azure/azure-cli)，它是使用 Python 为资源管理部署模型编写的下一代 CLI。
>
>

## <a name="managing-app-service-plans"></a>管理 App Service 计划
### <a name="create-an-app-service-plan"></a>创建 App Service 计划
若要创建应用服务计划，请使用 **azure appserviceplan create** 命令。

以下是各种参数的说明：

* **--resource-group**：包含新建应用服务计划的资源组。
* **--name**：应用服务计划的名称。
* **--location**：应用服务计划位置。
* **--sku**：所需的定价 SKU（选项包括：F1（免费）。 D1（共享）。 B1（基本，小）、B2（基本，中）、B3（基本，大）。 S1（标准，小）、S2（标准，中）、S3（标准，大）。 P1（高级，小）、P2（高级，中）、P3（高级，大）。）
* **--instances**：应用服务计划中的辅助角色数目（默认值为 1）。

使用此 cmdlet 的示例：

    azure appserviceplan create --name ContosoAppServicePlan --location "South Central US" --resource-group ContosoAzureResourceGroup --sku P1 --instances 10

#### <a name="create-a-linux-app-service-plan"></a>创建 Linux 应用服务计划

使用相同的 **azure appserviceplan create** 命令，附带额外参数 **--islinux true**。 请注意 [Linux 应用服务简介](app-service-linux-intro.md)中所述的限制和区域

### <a name="list-existing-app-service-plans"></a>列出现有的 App Service 计划
若要列出现有的应用服务计划，请使用 **azure appserviceplan list** 命令。

若要列出特定资源组之下的所有 App Service 计划，请使用：

    azure appserviceplan list --resource-group ContosoAzureResourceGroup

若要获取特定的应用服务计划，请使用 **azure appserviceplan show** 命令：

    azure appserviceplan show --name ContosoAppServicePlan --resource-group southeastasia

### <a name="configure-an-existing-app-service-plan"></a>配置现有的 App Service 计划
若要更改现有应用服务计划的设置，请使用 **azure appserviceplan config** 命令。 可以更改 SKU 和辅助角色数目 

    azure appserviceplan config --name ContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1 --instances 9

#### <a name="scaling-an-app-service-plan"></a>缩放 App Service 计划
若要缩放现有的 App Service 计划，请使用：

    azure appserviceplan config --name ContosoAppServicePlan --resource-group ContosoAzureResourceGroup --instances 9

#### <a name="changing-the-sku-of-an-app-service-plan"></a>更改应用服务计划的 SKU
若要更改现有应用服务计划的 SKU，请使用：

    azure appserviceplan config --name ContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1


### <a name="delete-an-existing-app-service-plan"></a>删除现有的 App Service 计划
若要删除现有的应用服务计划，请先移动或删除所有已分配的应用。 然后才可以使用 **azure webapp delete** 命令删除该应用服务计划。

    azure appserviceplan delete --name ContosoAppServicePlan --resource-group southeastasia

## <a name="managing-app-service-apps"></a>管理应用服务应用
### <a name="create-a-web-app"></a>创建 Web 应用
若要创建 Web 应用，请使用 **azure webapp create** 命令。

以下是各种参数的说明：

* **--name**：Web 应用的名称。
* **--plan**：用于托管 Web 应用的服务计划的名称。
* **--resource-group**：托管应用服务计划的资源组。
* **--location**：Web 应用的位置。

使用此 cmdlet 的示例：

    azure webapp create --name ContosoWebApp --resource-group ContosoAzureResourceGroup --plan ContosoAppServicePlan --location "South Central US"

### <a name="delete-an-existing-app"></a>删除现有应用
若要删除现有应用，可使用 **azure webapp delete** 命令。 需要指定应用名称和资源组名称。

    azure webapp delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="list-existing-apps"></a>列出现有应用
若要列出现有应用，请使用 **azure webapp list** 命令。

若要列出特定资源组中的所有应用，请使用：

    azure webapp list --resource-group ContosoAzureResourceGroup

若要获取特定应用，请使用 **azure webapp show** 命令。

    azure webapp show --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="configure-an-existing-app"></a>配置现有应用
若要更改现有应用的设置和配置，请使用 **azure webapp config set** 命令。

示例 (1)：更改应用的 php 版本 

    azure webapp config set --name ContosoWebApp --resource-group ContosoAzureResourceGroup --phpversion 5.6

示例 (2)：添加或更改应用设置

    webapp config appsettings set --name ContosoWebApp --resource-group ContosoAzureResourceGroup appsetting1=appsetting1value,appsetting2=appsetting2value

若要了解哪项其他的配置可以更改，请使用 **azure webapp config set -h** 命令。

### <a name="change-the-state-of-an-existing-app"></a>更改现有应用的状态
#### <a name="restart-an-app"></a>重启应用
若要重启应用，必须指定应用的名称和资源组。

    azure webapp restart --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="stop-an-app"></a>停止应用
若要停止应用，必须指定应用的名称和资源组。

    azure webapp stop --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="start-an-app"></a>启动应用
若要启动应用，必须指定应用的名称和资源组。

    azure webapp start --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="manage-publishing-profiles-of-an-app"></a>管理应用的发布配置文件
每个应用都具有可用于发布代码的发布配置文件。

#### <a name="get-publishing-profile"></a>获取发布配置文件
若要获取应用的发布配置文件，请使用：

    azure webapp publishingprofile --name ContosoWebApp --resource-group ContosoAzureResourceGroup

此命令会向命令行回显发布配置文件用户名和密码。

### <a name="manage-app-hostnames"></a>管理应用主机名
若要管理应用的主机名绑定，请使用 **azure webapp config hostnames** 命令  

#### <a name="list-hostname-bindings"></a>列出主机名绑定
若要获取应用的当前主机名绑定，请使用：

    azure webapp config hostnames list --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="add-hostname-bindings"></a>添加主机名绑定
若要将主机名绑定添加到应用，请使用：

    azure webapp config hostnames add --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

#### <a name="delete-hostname-bindings"></a>删除主机名绑定
若要删除主机名绑定，请使用：

    azure webapp config hostnames delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

## <a name="next-steps"></a>后续步骤
* 若要了解 Azure Resource Manager CLI 支持，请参阅 [Use the Azure CLI to manage Azure resources and resource groups](../azure-resource-manager/xplat-cli-azure-resource-manager.md)（使用 Azure CLI 管理 Azure 资源和资源组）。
* 若要了解如何使用 PowerShell 管理应用服务，请参阅 [Using Azure Resource Manager-Based PowerShell to Manage Azure Web Apps](app-service-web-app-azure-resource-manager-powershell.md)（使用基于 Azure Resource Manager 的 PowerShell 管理 Azure Web 应用）。
* 若要了解 Linux Azure App Service，请参阅 [Linux 应用服务简介](app-service-linux-intro.md)。

