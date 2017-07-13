使用 Azure CLI 获取 API 应用的远程部署 URL。 在以下命令中，将 *\<app_name>* 替换为 Web 应用的名称。

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

配置本地 Git 部署，使之能够推送到远程目标。

```bash
git remote add azure <URI from previous step>
```

推送到 Azure 远程功能以部署应用。 系统将提示你输入之前创建部署用户时创建的密码。 请确保输入之前在快速入门中创建的密码，而不是用于登录 Azure 门户的密码。

```bash
git push azure master
```
