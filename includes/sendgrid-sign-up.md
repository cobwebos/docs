Azure 客户每月可解锁 25,000 封免费电子邮件。 通过每月的这 25,000 封免费电子邮件，将可使用高级报告和分析以及[所有 API][all APIs]（Web、SMTP、事件、分析等）。 有关 SendGrid 提供的其他服务的信息，请访问 [SendGrid 解决方案][SendGrid Solutions]页。

### <a name="to-sign-up-for-a-sendgrid-account"></a>注册 SendGrid 帐户
1. 登录到 [Azure 门户][Azure portal]。
2. 在左侧菜单中，单击“创建资源”。

    ![command-bar-new][command-bar-new]
3. 单击“外接程序”，并单击“SendGrid 电子邮件传递”。

    ![sendgrid-store][sendgrid-store]
4. 完成注册表单，并选择“创建”。

    ![sendgrid-create][sendgrid-create]
5. 输入“名称”标识 Azure 设置中的 SendGrid 服务。 名称的长度必须介于 1 到 100 个字符之间，并只能包含字母字符、短划线、句点和下划线。 名称在订阅的 Azure 应用商店项目的列表中必须是唯一的。
6. 输入并确认“密码”。
7. 选择“订阅”。
8. 新建“资源组”或使用现有资源组。
9. 在“定价层”对话框中，选择要注册的 SendGrid 计划。

    ![sendgrid-pricing][sendgrid-pricing]
10. 如果有“促销代码”，请输入。
11. 输入“联系信息”。
12. 查看并接受“法律条款”。
13. 确认购买后，将出现“部署成功”弹出窗口，并在“所有资源”部分列出用户帐户。

    ![all-resources][all-resources]

    完成购买并单击“管理”按钮启动电子邮件验证过程后，用户将收到一封来自 SendGrid 的电子邮件，要求验证其帐户。 如果拒绝接收此电子邮件，或验证用户帐户时出错，请参阅此常见问题解答。

    ![manage][manage]

    **验证帐户之前，用户每天最多只能发送 100 封电子邮件。**

    若要修改订阅计划或查看 SendGrid 联系人设置，请单击 SendGrid 服务的名称以打开 SendGrid Marketplace 仪表板。

    ![设置][settings]

    若要使用 SendGrid 发送电子邮件，必须提供 API 密钥。

### <a name="to-find-your-sendgrid-api-key"></a>查找 SendGrid API 密钥
1. 单击“管理”。

    ![manage][manage]
2. 在 SendGrid 仪表板中，选择“设置”，并选择左侧菜单中的“API 密钥”。

    ![api-keys][api-keys]

3. 单击“创建 API 密钥”。

    ![general-api-key][general-api-key]
4. 至少提供“此密钥名称”和对“邮件发送”的完全访问权限，并选择“保存”。

    ![access][access]
5. 此时会显示一次 API。 请务必安全存储该 API。

### <a name="to-find-your-sendgrid-credentials"></a>查找 SendGrid 凭据
1. 单击密钥图标，查找“用户名”。

    ![key][key]
2. 密码是用户设置时选择的密码。 若需进行任何更改，可选择“更改密码”或“重置密码”。

若要管理电子邮件传递设置，请单击“管理”按钮。 此操作将重定向到 SendGrid 仪表板。

    ![manage][manage]

    For more information on sending email through SendGrid, visit the [Email API Overview][Email API Overview].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/new-addon.png
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid-store.png
[sendgrid-create]: ./media/sendgrid-sign-up/sendgrid-create.png
[sendgrid-pricing]: ./media/sendgrid-sign-up/sendgrid-pricing.png
[all-resources]: ./media/sendgrid-sign-up/all-resources.png
[manage]: ./media/sendgrid-sign-up/manage.png
[settings]: ./media/sendgrid-sign-up/settings.png
[api-keys]: ./media/sendgrid-sign-up/api-keys.png
[general-api-key]: ./media/sendgrid-sign-up/general-api-key.png
[access]: ./media/sendgrid-sign-up/access.png
[key]: ./media/sendgrid-sign-up/key.png

<!--Links-->

[SendGrid Solutions]: https://sendgrid.com/solutions
[Azure portal]: https://portal.azure.com
[SendGrid Getting Started]: http://sendgrid.com/docs
[SendGrid Provisioning Process]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[all APIs]: https://sendgrid.com/docs/API_Reference/index.html
[Email API Overview]: https://sendgrid.com/docs/API_Reference/Web_API_v3/Mail/index.html
