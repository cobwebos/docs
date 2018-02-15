## <a name="rest"></a>使用 REST API 进行部署 
 
可以使用[部署服务 REST API](https://github.com/projectkudu/kudu/wiki/REST-API) 将 .zip 文件部署到 Azure 中的应用。 只需将 POST 请求发送到 https://<app_name>.scm.azurewebsites.net/api/zipdeploy 即可。 POST 请求必须在消息正文中包含此 .zip 文件。 应用的部署凭据是通过使用 HTTP BASIC 身份验证在请求中提供的。 有关详细信息，请参阅 [.zip 推送部署参考](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)。 

下面的示例使用 cURL 工具发送包含.zip 文件的请求。 可以从 Mac 或 Linux 计算机上的终端或通过在 Windows 上使用 Bash 运行 cURL。 使用项目 .zip 文件的位置路径替换 `<zip_file_path>` 占位符。 同时，使用应用的唯一的名称替换 `<app_name>`。

使用部署凭据的用户名替换 `<deployment_user>` 占位符。 出现 cURL 提示时，键入密码。 若要了解如何设置应用的部署凭据，请参阅[设置和重置用户级别的凭据](../articles/app-service/app-service-deployment-credentials.md#userscope)。   

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

此请求从已上传的 .zip 文件触发推送部署。 可以使用 https://<app_name>.scm.azurewebsites.net/api/deployments 终结点查看当前和之前的部署，如以下 cURL 示例所示。 同样，使用应用的名称替换 `<app_name>`；使用部署凭据的用户名替换 `<deployment_user>`。

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```