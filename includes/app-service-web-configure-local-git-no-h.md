使用 [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#config-local-git) 命令配置 Web 应用的本地 Git 部署。

应用服务支持采用多种方式将内容部署到 Web 应用，例如 FTP、本地 Git、GitHub、Visual Studio Team Services 和 Bitbucket。 在此快速入门中，将使用本地 Git 进行部署。 这意味着将通过使用 Git 命令从本地存储库推送到 Azure 中的存储库来进行部署。 

在以下命令中，将 *\<app_name>* 替换为 Web 应用的名称。

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

输出格式如下：

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

`<username>` 是你在之前的步骤中创建的[部署用户](#configure-a-deployment-user)。

复制所示的 URI；在下一步中将使用它。
