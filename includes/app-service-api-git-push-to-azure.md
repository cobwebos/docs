使用 Azure CLI 来获取 API 应用的远程部署 URL。 在以下命令，将 *\<app_name >*与 web 应用程序的名称。

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

配置本地 Git 部署，以便能够将推送到远程。

```bash
git remote add azure <URI from previous step>
```

将推送到 Azure 远程设置以部署你的应用。 系统提示您先前在创建部署用户时创建的密码。 请确保你输入之前在快速入门中创建的密码和不用于登录到 Azure 门户的密码。

```bash
git push azure master
```
