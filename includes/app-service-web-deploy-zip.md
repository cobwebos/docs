## <a name="deploy-uploaded-zip-file"></a>部署已上传的 ZIP 文件

在 Cloud Shell 中使用 [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip) 命令将上传的 ZIP 文件部署到 Web 应用。 确保将 *\<app_name>* 替换为 Web 应用的名称。

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/myAppFiles.zip
```

此命令将 ZIP 文件中的文件和目录部署到默认的应用服务应用程序文件夹 (`\home\site\wwwroot`) 并重启应用。 如果配置了任何其他的自定义生成过程，则也会运行该过程。
