创建 App Service 计划与[az appservice 计划创建](/cli/azure/appservice/plan#create)命令。

[!INCLUDE [app-service-plan](app-service-plan.md)]

下面的示例创建名为 App Service 计划`myAppServicePlan`中**免费**定价层：

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

创建 App Service 计划后，则 Azure CLI 将显示类似于下面的示例信息：

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
```