在 Cloud Shell 中，使用 [az group create](/cli/azure/group#create) 命令创建资源组。

[!INCLUDE [resource group intro text](resource-group.md)]

以下示例在“西欧”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

通常在附近的区域中创建资源组和资源。 若要查看 Azure Web 应用的所有支持位置，请运行 `az appservice list-locations` 命令。 