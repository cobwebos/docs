## <a name="prerequisites"></a>必备条件
我们可以编写 CDN 管理代码之前，我们需要先完成一些准备工作，以便我们的代码进行交互与 Azure 资源管理器。  若要执行此操作，你需要：

* 创建一个资源组包含本教程中我们创建的 CDN 配置文件
* 配置 Azure Active Directory 的我们的应用程序提供身份验证
* 将权限应用于资源组，以便只有授权的用户从我们的 Azure AD 租户可以与我们的 CDN 配置文件进行交互

### <a name="creating-the-resource-group"></a>创建资源组
1. 登录到[Azure 门户](https://portal.azure.com)。
2. 单击**新建**左上角中的按钮，然后**管理**，和**资源组**。

    ![创建新的资源组](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. 调用你的资源组*CdnConsoleTutorial*。  选择你的订阅并选择你附近的位置。  如果您愿意，您可以单击**固定到仪表板**复选框可在门户中固定到仪表板中的资源组。  这将使更轻松地查找更高版本。  你所做选择后，单击**创建**。

    ![命名的资源组](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. 创建资源组，如果未将其固定到仪表板后，您可以通过单击查找它**浏览**，然后**资源组**。  单击资源组，以将其打开。  记下你**订阅 ID**。  我们将更高版本需要它。

    ![命名的资源组](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>创建 Azure AD 应用程序和应用权限
有两种方法来与 Azure Active Directory 的应用程序身份验证： 单个用户或服务主体。 服务主体是类似于 Windows 中的服务帐户。  而不是与 CDN 配置文件进行交互权限授予特定用户，我们将改为授予对服务主体的权限。  服务主体通常用于自动化的非交互式进程。  即使本教程编写一个交互式控制台应用程序，我们将重点的服务主体方法。

创建一个服务主体包含多个步骤，包括创建 Azure Active Directory 应用程序。  为此，我们将为[遵循本教程](../articles/resource-group-create-service-principal-portal.md)。

> [!IMPORTANT]
> 请务必遵循中的所有步骤[链接的教程](../articles/resource-group-create-service-principal-portal.md)。  它是*极其重要*你严格按所述完成。  请务必请注意你**租户 ID**，**租户域名**(通常*。 onmicrosoft.com*域除非你指定自定义的域)，**客户端 ID**，和**客户端身份验证密钥**，因为我们将需要这些更高版本。  要非常小心地保护你**客户端 ID**和**客户端身份验证密钥**，因为这些凭据可使用的任何人，作为服务主体执行操作。
>
> 当你获取到名为配置多租户应用程序的步骤时，请选择**否**。
>
> 当你到达步骤[分配到角色的应用程序](../articles/azure-resource-manager/resource-group-create-service-principal-portal.md#assign-application-to-role)，使用更早版本，我们创建的资源组*CdnConsoleTutorial*，但而不是**读取器**角色，分配**CDN 配置文件参与者**角色。  分配应用程序后**CDN 配置文件参与者**在资源组，返回到本教程上的角色。 
>
>

一旦创建服务主体并分配**CDN 配置文件参与者**角色，**用户**边栏选项卡为你的资源组应类似于此。

![用户边栏选项卡](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>交互式用户身份验证
如果，而不是服务主体，你希望让交互式单个用户身份验证，则过程是非常类似于服务主体。  事实上，你将需要按照相同的过程，但进行一些小的更改。

> [!IMPORTANT]
> 如果你要选择而不是服务主体使用单个用户身份验证，仅按照这些后续步骤。
>
>

1. 创建你的应用程序，而不时**Web 应用程序**，选择**本机应用程序**。

    ![本机应用程序](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. 在下一页上，您将被提示输入**重定向 URI**。  URI 不会进行验证，但请记住你的输入。  将为需要更高版本。
3. 若要创建无需**客户端身份验证密钥**。
4. 而不是分配到服务主体**CDN 配置文件参与者**角色，我们要将单个用户或组分配。  在此示例中，你可以看到，已分配*CDN 演示用户*到**CDN 配置文件参与者**角色。  

    ![单个用户访问](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
