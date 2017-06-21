## <a name="push-to-azure-from-git"></a>从 Git 推送到 Azure

将 Azure 远程功能添加到本地 Git 存储库。

```bash
git remote add azure <URI from previous step>
```

推送到 Azure 远程功能以部署应用。 系统将提示你输入之前创建部署用户时创建的密码。 请确保输入在[配置部署用户](#configure-a-deployment-user)中创建的密码，而不是用于登录到 Azure 门户的密码。

```bash
git push azure master
```

前面的命令将显示类似于以下示例的信息：
