使用 Azure CLI 获取 API 应用的远程部署 URL，然后配置本地 Git 部署，以便能够推送到远程。

```bash
giturl=$(az webapp deployment source config-local-git -n $app_name \ -g myResourceGroup --query [url] -o tsv)

git remote add azure $giturl
```

推送到 Azure 远程功能以部署应用。 系统将提示你输入之前创建部署用户时创建的密码。 请确保输入之前在快速入门中创建的密码，而不是用于登录 Azure 门户的密码。

```bash
git push azure master
```
