## <a name="configure-a-deployment-user"></a>配置部署用户  

对于 FTP 和本地 Git 部署，必须在服务器上配置一个部署用户，用于对部署进行身份验证。

> [!NOTE]
> 在 Web 应用中进行 FTP 和本地 Git 部署时需要一个部署用户。
> `username` 和 `password` 是帐户级别。 它们与 Azure 订阅凭据不同。
>

运行 [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) 命令创建部署凭据。

```azurecli
az appservice web deployment user set --user-name <username> --password <password>
```

用户名必须唯一，密码必须是强密码。 如果收到 ` 'Conflict'. Details: 409` 错误，请更改用户名。 如果收到 ` 'Bad Request'. Details: 400` 错误，请使用更强的密码。

只需创建此部署用户一次；可对所有 Azure 部署使用此用户。

请记下用户名和密码，因为稍后在部署应用时需要用到。