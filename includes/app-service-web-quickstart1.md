## <a name="create-a-resource-group"></a>创建资源组

 [Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)是一个逻辑容器，在其中托管 Web 应用和数据库等资源。 部署、更新和删除资源组中的资源。

使用 [az group create](/cli/azure/group#create) 命令创建资源组。

```azurecli
az group create --name myResourceGroup --location westeurope
```

若要查看可用位置，请使用 `az appservice list-locations` 命令。 你通常在附近的区域中创建资源。

## <a name="create-an-azure-app-service-plan"></a>创建 Azure 应用服务计划

使用 [az appservice plan create](/cli/azure/appservice/plan#create) 命令创建“免费”的[应用服务计划](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。

[!INCLUDE [app-service-plan](app-service-plan.md)]

以下命令使用**免费**定价层创建名为 `quickStartPlan` 的应用服务计划。

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku FREE
```

创建应用服务计划后，Azure CLI 将显示类似于以下示例的信息：

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "quickStartPlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "quickStartPlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
```

## <a name="create-a-web-app"></a>创建 Web 应用