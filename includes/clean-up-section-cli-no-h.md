在前面的步骤中，在资源组中创建了 Azure 资源。 如果认为将来不需要这些资源，可以通过删除资源组来删除它们。
 
1. 运行以下命令，确保该资源组不包含想要保存的任何资源：

  ```azurecli
  az group show --name myResourceGroup
  ```

2. 如果显示的资源都是想要删除的资源，则运行以下命令：
 
  ```azurecli
  az group delete --name myResourceGroup
  ```
