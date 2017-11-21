## <a name="clean-up-resources"></a>清理资源

在前面的步骤中，在资源组中创建了 Azure 资源。 如果认为将来不需要这些资源，请在 Cloud Shell 中运行以下命令删除资源组：

```azurecli-interactive
az group delete --name myResourceGroup
```

此命令可能需要花费一分钟时间运行。