使用 [az group create](/cli/azure/group#az_group_create) 命令创建资源组。

[!INCLUDE [resource group intro text](resource-group.md)]

以下示例在“westeurope”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

若要查看可用位置，请运行 `az appservice list-locations` 命令。 通常在附近的区域中创建资源。
