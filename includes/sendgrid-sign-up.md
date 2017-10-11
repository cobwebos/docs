Azure 客户每月可解锁 25,000 封免费电子邮件。 这些 25,000 封免费每月电子邮件将允许访问的高级报告和分析和[所有 Api] [ all APIs] （Web、 SMTP、 事件、 分析和的详细信息）。 有关 SendGrid 提供的其他服务的信息，请访问[SendGrid 解决方案][ SendGrid Solutions]页。

### <a name="to-sign-up-for-a-sendgrid-account"></a>若要注册的 SendGrid 帐户
1. 登录到[Azure 管理门户][Azure Management Portal]。
2. 在左侧菜单中，单击**新建**。

    ![命令栏新][command-bar-new]
3. 单击**外接程序**然后**SendGrid 电子邮件传递**。

    ![sendgrid 应用商店][sendgrid-store]
4. 完成注册表单，然后选择**创建**。

    ![sendgrid 的创建][sendgrid-create]
5. 输入**名称**以标识 Azure 设置中的 SendGrid 服务。 名称必须介于 1 和 100 个字符的长度之间，并且包含仅字母数字字符、 短划线、 点和下划线。 名称必须是唯一的订阅的 Azure 应用商店项目列表中。
6. 输入并确认你**密码**。
7. 选择你**订阅**。
8. 创建一个新**资源组**或使用现有。
9. 在**定价层**部分，选择你想要注册的 SendGrid 计划。

    ![sendgrid 定价][sendgrid-pricing]
10. 输入**促销代码**如果你有一个。
11. 输入你**联系信息**。
12. 查看并接受**法律条款**。
13. 确认你的购买后你将看到**部署成功**弹出窗口，你将看到你的帐户中列出**的所有资源**部分。

    ![所有资源][all-resources]

    在完成购买并单击后**管理**按钮来启动电子邮件验证过程，你将收到一封电子邮件来自 SendGrid 要求你验证你的帐户。 如果你不接收此电子邮件，或验证你的帐户的问题，请参阅此常见问题。

    ![管理][manage]

    **在验证你的帐户之前，你仅可以发送最多 100 个电子邮件/每日。**

    若要修改订阅计划或查看 SendGrid 联系人设置，请单击 SendGrid 服务以打开 SendGrid 应用商店仪表板的名称。

    ![设置][settings]

    若要发送电子邮件使用 SendGrid，必须提供你的 API 密钥。

### <a name="to-find-your-sendgrid-api-key"></a>若要查找你的 SendGrid API 密钥
1. 单击“管理” 。

    ![管理][manage]
2. SendGrid 仪表板，在选择**设置**然后**API 密钥**左侧菜单中。

    ![api 密钥][api-keys]

3. 单击**创建 API 密钥**下拉列表中，然后选择**常规 API 密钥**。

    ![常规 api 密钥][general-api-key]
4. 在最低限度上，提供**此密钥名称**并提供完全访问权限**邮件发送**和选择**保存**。

    ![访问][access]
5. 你的 API 将显示在此点一次。 请务必将其安全地存储。

### <a name="to-find-your-sendgrid-credentials"></a>查找 SendGrid 凭据
1. 单击密钥图标来查找你**用户名**。

    ![key][key]
2. 密码是你选择在安装程序。 你可以选择**更改密码**或**重置密码**需进行任何更改。

若要管理电子邮件传达能力设置，请单击**管理按钮**。 这将重定向到你的 SendGrid 仪表板。

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
[Azure Management Portal]: https://manage.windowsazure.com
[SendGrid Getting Started]: http://sendgrid.com/docs
[SendGrid Provisioning Process]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[all APIs]: https://sendgrid.com/docs/API_Reference/index.html
[Email API Overview]: https://sendgrid.com/docs/API_Reference/Web_API_v3/Mail/index.html
