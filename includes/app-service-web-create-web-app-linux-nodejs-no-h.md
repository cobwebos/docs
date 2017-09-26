在 Cloud Shell 中，使用 [az webapp create](/cli/azure/webapp#create) 命令在 `myAppServicePlan` 应用服务计划中创建一个 [Web 应用](../articles/app-service/containers/app-service-linux-intro.md)。 不要忘记将 `<app name>` 替换为唯一的应用名称。

以下命令中的运行时设置为 `NODE|8.1`。 若要查看所有受支持的运行时，请运行 [az webapp list-runtimes](/cli/azure/webapp#list-runtimes)。 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --runtime "NODE|8.1" --deployment-local-git
```

创建 Web 应用后，Azure CLI 会显示类似于以下示例的输出：

```json
Local git is configured with url of 'https://<username>@<app name>.scm.azurewebsites.net/<app name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

已在 Linux 容器中创建了空的新 Web 应用并启用了 Git 部署。

> [!NOTE]
> Git 远程的 URL 将显示在 `deploymentLocalGitUrl` 属性中，其格式为 `https://<username>@<app name>.scm.azurewebsites.net/<app name>.git`。 保存此 URL，因为稍后将需要它。
>
