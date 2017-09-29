在 Cloud Shell 中，使用 [az webapp deployment user set](/cli/azure/webapp/deployment/user#set) 命令创建部署凭据。

对 Web 应用进行 FTP 和本地 Git 部署时需要一个部署用户。 用户名和密码都为帐户级别。 它们与 Azure 订阅凭据不同。

在以下命令中，将 \<username> 和 \<password> 替换为新的用户名和密码。 用户名必须唯一。 密码长度必须至少为 8 个字符，其中包含以下 3 种元素中的两种：字母、数字、符号。 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

如果收到 ` 'Conflict'. Details: 409` 错误，请更改用户名。 如果收到 ` 'Bad Request'. Details: 400` 错误，请使用更强的密码。

只创建此部署用户一次；可对所有 Azure 部署使用此用户。

> [!NOTE]
> 记录用户名和密码。 之后需要它们来部署 Web 应用。
>
>