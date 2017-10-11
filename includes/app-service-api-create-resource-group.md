创建资源组与[az 组创建](/cli/azure/group#create)命令。

[!INCLUDE [resource group intro text](resource-group.md)]

下面的示例创建一个名为的资源组*myResourceGroup*中*westeurope*位置。

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

若要查看可用的位置，请运行`az appservice list-locations`命令。 你通常在你附近的区域中创建资源。
