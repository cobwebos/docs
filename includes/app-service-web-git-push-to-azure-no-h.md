在本地终端窗口中，将 Azure 远程功能添加到本地 Git 存储库。 将 _&lt;paste\_copied\_url\_here>_ 替换为从[创建 Web 应用](#create)保存的 Git 远程 URL。

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

使用以下命令推送到 Azure 远程功能以部署应用。 提示输入密码时，请确保输入在[配置部署用户](#configure-a-deployment-user)中创建的密码，而不是用于登录到 Azure 门户的密码。

```bash
git push azure master
```

此命令可能需要花费几分钟时间运行。 运行时，该命令会显示类似于以下示例的信息：
