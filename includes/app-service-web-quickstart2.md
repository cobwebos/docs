使用 [az appservice web create](/cli/azure/appservice/web#create) 命令创建该 Web 应用。 在以下命令中，请将 `<app_name>` 占位符替换为唯一应用名称。 `<app_name>` 用于 Web 应用的默认 DNS 站点中。 如果 `<app_name>` 不唯一，你将收到友好错误消息“具有给定名称 <app_name> 的网站已存在”。

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
```

创建 Web 应用后，Azure CLI 将显示类似于以下示例的信息：

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<app_name>.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    < JSON data removed for brevity. >
}
```

浏览到该站点 (`http://<app_name>.azurewebsites.net`) 来查看新建的 Web 应用。

![app-service-web-service-created](../articles/app-service-web/media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)


## <a name="configure-local-git-deployment"></a>配置本地 Git 部署

应用服务支持采用多种方式将内容部署到 Web 应用，例如 FTP、本地 Git、GitHub、Visual Studio Team Services 和 Bitbucket。 

在此快速入门中，将使用本地 Git 进行部署。 这意味着将通过使用 Git 命令从本地存储库推送到 Azure 中的存储库来进行部署。 

请使用 [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) 命令配置本地 Git 对 Web 应用的访问权限。

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

输出格式如下：

```
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

保存所显示的 URI。 在下一步骤中将使用它。 `<username>` 是你在之前的步骤中创建的[部署用户](#configure-a-deployment-user)。

## <a name="push-to-azure-from-git"></a>从 Git 推送到 Azure

将 Azure 远程功能添加到本地 Git 存储库。

```bash
git remote add azure <URI from previous step>
```

推送到 Azure 远程功能以部署应用。 系统将提示你输入之前创建部署用户时创建的密码。 请确保输入在[配置部署用户](#configure-a-deployment-user)中创建的密码，而不是用于登录到 Azure 门户的密码。

```azurecli
git push azure master
```

前面的命令将显示类似于以下示例的信息：

```bash
Counting objects: 13, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (11/11), done.
Writing objects: 100% (13/13), 2.07 KiB | 0 bytes/s, done.
Total 13 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'cc39b1e4cb'.
remote: Generating deployment script.
remote: Generating deployment script for Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
remote: Deleting file: 'hostingstart.html'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-app"></a>浏览到应用


浏览到所部署的应用：

```
http://<app_name>.azurewebsites.net
```

