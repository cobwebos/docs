<!-- not suitable for Mooncake -->

<properties 
	pageTitle="Azure 中的 Web 应用入门 - 第 2 部分" 
	description="单击几下鼠标即可将操作功能添加到 Azure 中的 Web 应用。" 
	services="app-service\web"
	documentationCenter=""
	authors="cephalin" 
	manager="wpickett" 
	editor="" 
/>

<tags
	ms.service="app-service-web"
	ms.date="03/17/2016"
	wacn.date=""/>

# Azure 入门 - 第 2 部分

在 [Azure Web 应用入门](/documentation/articles/app-service-web-get-started)中，你已将一个 Web 应用部署到 Azure，所有设置均可快速轻松更新。在本文中，你将快速地将一些重要的功能添加到所部署的应用，使其保持理想状态。这样，你将了解 App Service 不仅仅是网站托管程序。它可为绝佳的应用提供企业级功能，以便将创意活力集中在以应用取悦用户，而不在于符合真实世界的安全性、可缩放性、性能、易管理性等要求。

只需按几下鼠标，你就可以了解如何：

- 强制实施用户身份验证
- 自动缩放应用
- 接收应用的性能警报

无论在前一篇文章中部署哪一个示例应用，都可以遵循以下的操作。

## 对用户进行身份验证

现在，让我们看看将身份验证添加到应用有多么容易。

1. 在应用的边栏选项卡（刚打开）中，单击“设置”>“身份验证/授权”。  
    ![身份验证 - 设置边栏选项卡](./media/app-service-web-get-started/aad-login-settings.png)
    
2. 单击“打开”以打开身份验证。  
    ![身份验证 - 打开](./media/app-service-web-get-started/aad-login-auth-on.png)
    
4. 在“身份验证提供程序”中，单击“Azure Active Directory”。  
    ![身份验证 - 选择 Azure AD](./media/app-service-web-get-started/aad-login-config.png)

5. 在“Azure Active Directory 设置”边栏选项卡中，单击“快速”，然后单击“确定”。默认设置在默认目录中创建新的 Azure AD 应用程序。  
    ![身份验证 - 快速配置](./media/app-service-web-get-started/aad-login-express.png)

6. 单击“保存”。  
    ![身份验证 - 保存配置](./media/app-service-web-get-started/aad-login-save.png)

    成功更改之后，你将看到通知铃铛变成绿色，以及一条友好的消息。

7. 返回应用的主边栏选项卡，单击“URL”链接（或菜单列中的“浏览”）。此链接是 HTTP 地址。  
    ![身份验证 - 浏览到 URL](./media/app-service-web-get-started/aad-login-browse-click.png)  
    但是在新的选项卡中打开应用后，URL 框将重定向数次并通过 HTTPS 地址到达应用。你将看到已使用 Azure 订阅登录 Microsoft 帐户并且已使用该帐户自动登录应用。
    ![身份验证 - 已登录](./media/app-service-web-get-started/aad-login-browse-http-postclick.png)  
    因此，如果你现在打开另一个浏览器（以确保尚未登录），将在导航到相同应用的 URL 时看到登录画面：  
    ![身份验证 - 登录页](./media/app-service-web-get-started/aad-login-browse.png)  
    如果你从未使用过 Azure Active Directory，默认目录可能没有任何 Azure AD 用户。在此情况下，那里唯一的帐户可能是你以 Azure 订阅登录的 Microsoft 帐户。这就是为什么前面能在相同的浏览器中自动登录此应用。
    你也可以使用相同的 Microsoft 帐户在此登录页面上登录。

祝贺你，现在你可以对传入站点的所有流量进行身份验证。

你可能已注意到在“身份验证/授权”边栏选项卡中能做的不止于此，例如：

- 启用社交登录
- 启用多个登录选项
- 更改当用户首次导航到应用时的默认行为

Azure 针对某些常见的身份验证要求提供周全的解决方案，所以不需要自行提供身份验证逻辑。 
有关详细信息，请参阅 [Azure 身份验证/授权](/blog/announcing-app-service-authentication-authorization/)。

## 向上和向外缩放应用

接下来，让我们缩放应用。缩放 Azure Web 应用的方法有两种：

- [向上缩放](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)：向上缩放 Azure Web 应用时，将更改应用所属的 App Service 计划的定价层。除了更多的 CPU、内存和磁盘空间，向上缩放还可让拥有其他的功能，例如专用 VM 实例、自动缩放，99.95% 的 SLA、自定义域、自定义 SSL 证书、部署槽、备份、还原，等等。更高的层可提供更多功能给 Azure Web 应用。  
- [向外缩放](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)：向外缩放 Azure Web 应用时，将更改应用（或相同 App Service 计划中的应用）运行所在的 VM 实例数。在标准层或更高层，你可以启用根据性能度量值自动缩放 VM 实例。 

不再赘述，让我们马上为应用设置自动缩放。

1. 首先，向上缩放以启用自动缩放。在应用的边栏选项卡中，单击“设置”>“向上缩放(App Service 计划)”。  
    ![向上缩放 - 设置边栏选项卡](./media/app-service-web-get-started/scale-up-settings.png)

2. 卷动并选择“S1 标准”层（这是支持自动缩放的最低层（下面所圈选）），然后单击“选择”。  
    ![向上缩放 - 选择定价层](./media/app-service-web-get-started/scale-up-select.png)

    现已完成向上缩放。
    
    >[AZURE.IMPORTANT] 这一层将消耗试用额度。如果你使用即用即付帐户，将从你的帐户中扣费。
    
3. 接下来，配置自动缩放。在应用的边栏选项卡中，单击“设置”>“向外缩放(App Service 计划)”。
    ![向外缩放 - 设置边栏选项卡](./media/app-service-web-get-started/scale-out-settings.png)

4. 将“缩放依据”更改为“CPU 百分比”。下拉列表下面的滑块会相应地变化。然后，定义介于 “1” 与 “2” 之间的“实例”范围，以及介于 40 与 80 之间的“目标范围”。为此，可以在框中键入值或移动滑块。
    ![向外缩放 - 配置自动缩放](./media/app-service-web-get-started/scale-out-configure.png)
    
    根据此配置，当 CPU 使用率高于 80% 时，应用将自动向外缩放；当 CPU 使用率低于 40% 时则缩小。
    
5. 单击菜单栏中的“保存”。

祝贺你，你的应用已在自动缩放。

你可能已注意到在“缩放设置”边栏选项卡中能做的不止于此，例如：

- 手动缩放到特定的实例数
- 根据其他性能度量值（例如内存百分比或磁盘队列）进行缩放
- 自定义触发性能规则时的缩放行为
- 按计划自动缩放
- 设置未来事件的自动缩放行为

有关向上缩放应用的详细信息，请参阅[在 Azure Web 应用中缩放定价层](/documentation/articles/app-service-scale)。有关向外缩放的详细信息，请参阅[手动或自动缩放实例计数](/documentation/articles/insights-how-to-scale)。

## 接收应用的警报

既然你的应用正在自动缩放，当它达到最大实例计数 (2) 且 CPU 使用率超过所需的百分比 (80%) 时，会发生什么情况？ 
例如，你可以设置警报来通知这种情况，以便进一步向上缩放应用。让我们快速为此方案设置警报。

1. 在应用的边栏选项卡中，单击“工具”>“警报”。  
    ![警报 - 设置边栏选项卡](./media/app-service-web-get-started/alert-settings.png)

2. 单击“添加警报”。然后，在“资源”框中，选择以 “(serverfarms)” 结尾的资源。这是你的 App Service 计划。  
    ![警报 - 为 App Service 计划添加警报](./media/app-service-web-get-started/alert-add.png)

3. 将“名称”指定为 `CPU Maxed`、将“度量值”指定为“CPU 百分比”，将“阈值”指定为 `90`，选择“电子邮件所有者、参与者和阅读者”，然后单击“确定”。  
    ![警报 - 配置警报](./media/app-service-web-get-started/alert-configure.png)
    
    当 Azure 完成警报创建时，你会在“警报”边栏选项卡中看到该警报。  
    ![警报 - 完成的视图](./media/app-service-web-get-started/alert-done.png)

祝贺你，你现在可以接收警报。

此警报设置现在每隔 5 分钟检查一次 CPU 使用率。如果该数字高于 90%，以及获得授权的人员都收到电子邮件警报。若要查看有权接收警报的人员，请回到应用的边栏选项卡，然后单击“访问”按钮。  
![查看谁会收到警报](./media/app-service-web-get-started/alert-rbac.png)

你应会看到“订阅管理员”已是应用的“所有者”。如果你是 Azure 订阅（如试用订阅）的帐户管理员，此组就会将你包括在内。有关 Azure 基于角色的访问控制的详细信息，请参阅 [Azure 基于角色的访问控制](/documentation/articles/role-based-access-control-configure)。

## 后续步骤

当你配置警报时，可能已注意到“工具”边栏选项卡中有一组丰富的工具。这些工具可让你排查问题、监视性能、测试漏洞、管理资源、与 VM 控制台交互，以及添加有用的扩展。我们希望你单击每个工具，以探索这些简单却功能强大的工具。

此外，请了解如何使用部署的应用执行更多操作。以下是一份不完整的列表：

- [购买和配置自定义域名](/documentation/articles/custom-dns-web-site-buydomains-web-app)
- [设置过渡环境](/documentation/articles/web-sites-staged-publishing)
- [设置连续部署](/documentation/articles/web-sites-publish-source-control)
- [备份应用](/documentation/articles/web-sites-backup)
- [启用诊断日志](/documentation/articles/web-sites-enable-diagnostic-log)
- [访问本地资源](/documentation/articles/web-sites-hybrid-connection-get-started)
- [了解 Azure 的工作原理](/documentation/articles/app-service-how-works-readme)

<!-----HONumber=Mooncake_0328_2016--->