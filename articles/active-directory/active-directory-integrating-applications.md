<properties
   pageTitle="将应用程序与 Azure Active Directory 集成 | Microsoft Azure"
   description="有关如何在 Azure Active Directory (Azure AD) 中添加、更新或删除应用程序的详细信息。"
   services="active-directory"
   documentationCenter=""
   authors="msmbaldwin"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"

   ms.date="11/24/2015"
   wacn.date="" />

# 将应用程序与 Azure Active Directory 集成

[AZURE.INCLUDE [active-directory-devguide](../includes/active-directory-devguide)]

企业开发人员和软件即服务 (SaaS) 提供商可以开发能够与 Azure Active Directory (Azure AD) 集成的商业云服务或业务线应用程序，以针对其服务提供安全的登录和授权。若要将应用程序或服务与 Azure AD 进行集成，开发人员必须首先使用 Azure 管理门户向 Azure AD 注册关于其应用程序的详细信息。

本文说明如何在 Azure AD 中添加、更新或删除应用程序。你将了解有关可与 Azure AD 集成的不同类型的应用程序，以及如何将应用程序配置为访问其他资源（如 Web API 等等）。

有关应用程序属性的详细信息，请参阅[应用程序对象和服务主体对象](active-directory-application-objects.md)；若要了解在使用 Azure Active Directory 开发应用程序时应使用的品牌准则，请参阅[适用于集成应用的品牌准则](active-directory-branding-guidelines.md)；[了解 Azure Active Directory 应用程序清单](active-directory-application-manifest)中解释了应用程序清单。

## 添加应用程序

首先必须在目录中注册要使用 Azure AD 功能的任何应用程序。此注册过程涉及到提供有关应用程序的 Azure AD 详细信息，例如，该应用程序所在位置的 URL、对用户进行身份验证后用于发送答复的 URL、用于标识应用程序的 URI，等等。

如果你正在生成的 Web 应用程序只要求用户在 Azure AD 中登录，则你只需按照以下说明操作。如果你的应用程序需要访问 Web API、你正在生成需要访问 Web API 的本机应用程序，或者你想要使应用程序成为多租户应用程序，则需要先阅读[更新应用程序](#updating-an-application)部分的内容，然后继续配置你的应用程序。

如果你想要使应用程序可用于其他组织，则还需要在添加应用程序后启用外部访问。

### 在 Azure 管理门户中注册应用程序

1. 登录到 Azure 管理门户。

1. 在左侧菜单中单击“Active Directory”图标，然后单击所需的目录。

1. 在顶部菜单中单击“应用程序”。如果尚未将任何应用添加到你的目录，则此页只会显示“添加应用”链接。单击该链接，或者单击命令栏上的“添加”按钮。

1. 在“要执行什么操作”页上，单击“添加我的组织正在开发的应用程序”链接。

1. 在“向我们说明你的应用程序”页上，必须指定应用程序的名称并指明你要注册到 Azure AD 的应用程序类型。你可以选择“Web 应用程序和/或 Web API”（默认值）或“本机客户端应用程序”，后者表示电话或计算机等设备上安装的应用程序。完成后，单击页面右下角的箭头图标。

1. 在“应用程序属性”页上，提供 Web 应用程序的“登录 URL”和“应用 ID URI”（或者只提供本机客户端应用程序的“重定向 URI”），然后单击页面底部右下角的复选框。

1. 现已添加你的应用程序，此时你将转到应用程序的“快速启动”页。根据你的应用程序是 Web 应用程序还是本机应用程序，你将看到有关如何向应用程序添加更多功能的不同选项。添加应用程序后，你可以开始更新应用程序，使用户能够登录、访问其他应用程序中的 Web API，或者配置多租户应用程序（允许其他组织访问你的应用程序）。

>[AZURE.NOTE]默认情况下，新建的应用程序注册已配置为允许你的目录中的用户登录到你的应用程序。

## 更新应用程序

将应用程序注册到 Azure AD 后，可能需要更新该应用程序，以提供对 Web API 的访问权限、使其可在其他组织中使用，等等。本部分介绍如何进一步配置你的应用程序。有关如何在 Azure AD 中进行身份验证的详细信息，请参阅 [Azure AD 的身份验证方案](active-directory-authentication-scenarios)。

### 同意框架概述

使用 Azure AD 的新同意框架，可以轻松开发需要访问 Azure AD 保护的 Web API 的 Web 应用程序和本机应用程序。这些 Web API 包括图形 API、Office 365 和其他 Microsoft 服务，以及你自己的 Web API。该框架基于某个用户或管理员，该用户或管理员允许某个应用程序在其目录中注册，这可能涉及到访问目录数据。例如，如果某个 Web 应用程序需要调用 Office 365 的 Web API 以读取有关用户的日历信息，则该用户需要同意该应用程序。同意后，该 Web 应用程序能够代表该用户调用 Office 365 Web API，并根据需要使用日历信息。

同意框架使用公共或机密客户端，建立在 OAuth 2.0 及其各种流程的基础之上，例如，代码授权和客户端凭据授权。通过使用 OAuth 2.0，可以在 Azure AD 中生成多种不同类型的客户端应用程序（例如手机、平板电脑、服务器或 Web 应用程序），并获取对所需资源的访问权限。

有关同意框架的更多详细信息，请参阅 [Azure AD 中的 OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)、[Azure AD 的身份验证方案](active-directory-authentication-scenarios.md)和 Office 365 主题 [使用通用同意框架进行身份验证和授权](https://msdn.microsoft.com/library/office/dn605895(v=office.15).aspx)。

#### 同意体验示例

以下步骤将说明同意体验对应用程序开发人员和用户的工作方式。

1. 在 Azure 管理门户中应用程序的配置页上，使用“对其他应用程序的权限”控件中的下拉菜单设置你的应用程序所需的权限。

    ![针对其他应用程序的权限](./media/active-directory-integrating-applications/permissions.png)

1. 考虑已更新应用程序的权限，该应用程序正在运行，并且某个用户即将首次使用该应用程序。如果应用程序尚未获取访问令牌或刷新令牌，则该应用程序需要转到 Azure AD 的授权终结点，以获取可用于取得新访问令牌和刷新令牌的授权代码。

1. 如果用户尚未经过身份验证，系统将要求他们登录到 Azure AD。

    ![用户或管理员登录到 Azure AD](./media/active-directory-integrating-applications/useradminsignin.png)

1. 用户登录后，Azure AD 将决定是否要向该用户显示同意页。此决定基于该用户（或其组织的管理员）是否已授予应用程序许可。如果尚未授予许可，Azure AD 将提示用户授予许可，并显示运行该应用程序所需的权限。同意对话框中显示的权限集与在 Azure 管理门户中“对其他应用程序的权限”控件中选择的权限集相同。

    ![用户同意体验](./media/active-directory-integrating-applications/userconsent.png)

1. 用户授予许可后，授权代码将返回到你的应用程序，应用程序可凭此获取访问令牌和刷新令牌。有关此流程的详细信息，请参阅 [Azure AD 的身份验证方案](active-directory-authentication-scenarios.md)主题中的[从 Web 应用程序到 Web API](active-directory-authentication-scenarios.md#web-application-to-web-api) 部分。

### 访问其他应用程序中的 Web API

使用上述同意框架，你可以将应用程序配置为要求使用权限来访问你的目录中的 Web API 公开的数据。默认情况下，所有应用程序可以从 Azure Active Directory（图形 API）和 Azure 服务管理 API 中选择权限。默认情况下已选择 Azure AD 的“启用登录并读取用户的配置文件”权限。如果你的目录还包含 Office 365 订阅，则还可以选择 SharePoint 和 Exchange Online 的 Web API 和权限。可以从所需 Web API 旁边的下拉菜单中的两种类型的权限中进行选择：

- 应用程序权限：你的应用程序需要亲自直接访问 Web API（无用户上下文）。此类型的权限需要管理员同意，并且不可用于本机客户端应用程序。

- 委托权限：你的应用程序需要以登录用户的身份访问 Web API，但访问权限受所选权限的限制。除非权限已配置为需要管理员同意，否则用户可以授予此类型的权限。

#### 添加对其他应用程序中 Web API 的访问权限

1. 登录到 Azure 管理门户。

1. 在左侧菜单中单击“Active Directory”图标，然后单击所需的目录。

1. 在顶部菜单中，单击“应用程序”，然后单击要配置的应用程序。“快速启动”页将显示单一登录信息和其他配置信息。

1. 展开“快速启动”的“访问其他应用程序中的 Web API”部分，然后单击“选择权限”部分下的“立即配置它”链接。此时将显示应用程序属性页。

1. 向下滚动到“对其他应用程序的权限”部分。在第一列中，你可以从目录中提供的、公开 Web API 的应用程序中进行选择。选择后，你可以选择 Web API 公开的应用程序和委托权限。

1. 选择后，单击命令栏上的“保存”按钮。

>[AZURE.NOTE]单击“保存”按钮还会基于你配置的“对其他应用程序的权限”自动设置对你目录中的应用程序的权限。可以在应用程序的“属性”选项卡中查看这些应用程序权限。

### 向其他应用程序公开 Web API

你可以开发一个 Web API，并通过向其他应用程序开发人员公开你的权限作用域，来使该 Web API 可供其他组织使用。可以像提供其他 Microsoft Web API（包括图形 API 和 Office 365 API）一样提供正确配置的 Web API。通过配置[应用程序清单](active-directory-application-manifest)来提供 Web API，该清单是表示应用程序标识配置的 JSON 文件。若要公开权限作用域，请在 Azure 管理门户中导航到你的应用程序，然后单击命令栏上的“应用程序清单”按钮。有关详细信息，请参阅[了解 Azure Active Directory 应用程序清单](active-directory-application-manifest)。

#### 向其他应用程序公开 Web API

1. 登录到 Azure 管理门户。

1. 在左侧菜单中单击“Active Directory”图标，然后单击所需的目录。

1. 在顶部菜单中，单击“应用程序”，然后单击要配置的应用程序。“快速启动”页将显示单一登录信息和其他配置信息。

1. 在命令栏中单击“管理清单”按钮，然后选择“下载清单”。

1. 打开 JSON 应用程序清单文件，并将 “oauth2Permissions” 节点替换为以下 JSON 代码段。此代码段演示了如何公开称为用户模拟的权限作用域。请确保更改你自己的应用程序的文本和值：

		"oauth2Permissions": [
		{
			"adminConsentDescription": "Allow the application full access to the Todo List service on behalf of the signed-in 	user",
			"adminConsentDisplayName": "Have full access to the Todo List service",
			"id": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
			"isEnabled": true,
			“origin”: “Application”
			"type": "User",
			"userConsentDescription": "Allow the application full access to the todo service on your behalf",
			"userConsentDisplayName": "Have full access to the todo service",
			"value": "user_impersonation"
			}
		],

    id 值必须是你通过使用 GUID 生成工具或以编程方式新生成的 GUID。它表示 Web API 公开的权限的唯一标识符。将客户端适当地配置为请求访问 Web API 并调用 Web API 后，该客户端将显示一个 OAuth 2.0 JWT 令牌，该令牌的作用域 (scp) 声明已设置为上面指定的值（在本例中为 user\_impersonation）。

	>[AZURE.NOTE]以后你可以根据需要公开其他权限作用域。请考虑你的 Web API 可能要公开与各种不同功能关联的多个权限。现在，你可以通过使用所收到的 OAuth 2.0 JWT 令牌中的作用域 (scp) 声明来控制到 Web API 的访问。

1. 保存更新的 JSON 文件，然后将其上载，方法是单击命令栏中的“管理清单”按钮，选择“上载清单”，浏览到更新的清单文件，然后将其选中。上载后，你的 Web API 便已配置为可供目录中的其他应用程序使用。

#### 验证 Web API 是否已公开给目录中的其他应用程序

1. 在顶部菜单中，单击“应用程序”，选择你要为其配置 Web API 访问权限的所需应用程序，然后单击“配置”。

1. 向下滚动到“对其他应用程序的权限”部分。单击“选择应用程序”下拉菜单，然后便可以选择你刚刚为其公开了权限的 Web API。从“委托权限”下拉菜单中选择新的权限。

![显示“待办事项”权限](./media/active-directory-integrating-applications/listpermissions.png)

#### 应用程序清单 JSON 文件的 AppPermissions 架构

下表列出了应用程序清单 JSON 文件的 oauth2Permissions 部分的可能值。

|元素|说明|
|---|---|
|adminConsentDescription|将鼠标悬停在管理员同意对话框以及同意的应用程序的属性页中时显示的帮助说明。|
|adminConsentDisplayName|在应用程序和/或委托权限作用域下拉列表中显示的、同意期间向管理员显示的以及在应用程序的属性页中显示的权限友好名称。|
|id|表示权限作用域的唯一内部标识符。该标识符在应用程序的所有权限中必须唯一，并且必须是一个 GUID。|
|isEnabled|在创建或更新 OAuth2 权限时，请始终将此值设置为 true。如果你要删除某个权限，则必须先将此值设置为 false 并上载清单。然后，可以在后续的清单上载操作中删除该权限。|
|origin|保留供将来使用。始终设置为“application”。|
|type|可以是下列值之一：“User”：可由最终用户同意。“Admin”：必须由公司管理员同意|
|userConsentDescription|将鼠标悬停在用户的同意对话框中时显示的帮助说明。|
|userConsentDisplayName|同意期间向最终用户显示的以及在应用程序访问面板中的应用程序属性页上显示的权限友好名称。|
|value|如果用户已同意此特定权限，此值将放入 OAuth 2.0 访问令牌的 scp 声明。在模拟用户时，Web API 可以使用此值来确定应用程序具有的访问级别的作用域。该值不能包含任何空格，并且在应用程序的所有权限中必须唯一。|

### 访问图形 API

本部分介绍如何将应用程序更新为访问图形 API（在“对其他应用程序的权限”列表中称为“Azure Active Directory”）。默认情况下，向 Azure AD 注册的所有应用程序都可以使用该 API。可以从图形 API 的以下权限中进行选择：

|权限名称|说明|类型|
|---|---|---|
|启用登录并读取用户的配置文件|允许用户使用其组织帐户登录到应用程序，并允许应用程序读取已登录用户的配置文件，例如其电子邮件地址和联系信息。|仅限委托权限。可由用户同意。|
|访问组织的目录|允许应用程序代表已登录用户访问你的组织的目录。|仅限委托权限。可由本机客户端中的用户同意，对于 Web 应用程序，只能由管理员同意。|
|读取目录数据|允许应用程序读取你的组织的目录中的数据，例如用户、组和应用程序。|委托和应用程序的权限。必须由管理员同意。|
|读取和写入目录数据|允许应用程序读取和写入你的组织的目录中的数据，例如用户和组。|委托和应用程序的权限。必须由管理员同意。|

对于 Azure 管理门户的现有用户，通过新的“对其他应用程序的权限”控件设置“读取目录数据”和“读取和写入目录数据”应用程序权限等同于使用以前的“管理访问权限”向导。若要查看 Office 365 公开的权限作用域，请参阅[使用通用同意框架进行身份验证和授权](https://msdn.microsoft.com/office/office365/howto/common-app-authentication-tasks)主题。

>[AZURE.NOTE]由于当前存在的限制，如果本机客户端应用程序使用“访问组织的目录”权限，则它们只能调用 Azure AD 图形 API。此限制不适用于 Web 应用程序。

### 配置多租户应用程序

将应用程序添加到 Azure AD 时，你可以希望你的应用程序只能由你组织中的用户访问。或者，你可能希望你的应用程序可由外部组织中的用户访问。这两种应用程序分别称为单租户应用程序和多租户应用程序。你可以修改单租户应用程序的配置，使其成为多租户应用程序，本部分将对此进行介绍。

请务必注意单租户应用程序与多租户之间的差别。单租户应用程序预定在一家组织中使用。它们通常是企业开发人员编写的业务线 (LoB) 应用程序。单租户应用程序只需要供单个目录中的用户进行访问，因此，只需要将其设置在单个目录中。多租户应用程序预定在多家组织中使用。它们通常是独立软件供应商 (ISV) 编写的软件即服务 (SaaS) 应用程序。多租户应用程序需要设置在将使用它们的每个目录中，需要经过用户或管理员许可才能注册它们。

#### 使外部用户能够授予访问权限

如果你正在编写一个要供组织外部的客户或合作伙伴使用的应用程序，则需要在 Azure 管理门户中更新应用程序定义。

>[AZURE.NOTE]在启用外部访问时，必须确保应用程序的应用 ID URI 属于已验证的域。此外，返回 URL 必须以 https:// 开头。有关详细信息，请参阅[应用程序对象和服务主体对象](active-directory-application-objects)。

##### 为外部用户启用对你的应用程序的访问权限

1. 登录到 Azure 管理门户。

1. 在左侧菜单中单击“Active Directory”图标，然后单击所需的目录。

1. 在顶部菜单中，单击“应用程序”，然后单击要配置的应用程序。“快速启动”页将显示单一登录信息和其他配置信息。

1. 展开“快速启动”的“配置多租户应用程序”部分，然后单击“启用访问权限”部分中的“立即配置它”链接。此时将显示应用程序属性页。

1. 单击“应用程序是多租户的”旁边的“是”按钮，然后单击命令栏上的“保存”按钮。

完成上述更改后，其他组织中的用户和管理员可以向你的应用程序授予对其目录和其他数据的访问权限。

### 使用同意框架授予访问权限

若要使用同意框架授予访问权限，客户端应用程序必须使用 OAuth 2.0 请求授权。我们提供了[代码示例](https://github.com/AzureADSamples)用于说明 Web 应用程序、本机应用程序或服务器/后台应用程序如何请求授权代码和访问令牌来调用 Web API。

Web 应用程序可为用户提供注册体验。如果你确实提供注册体验，用户应会单击注册（或登录）按钮，此时会将浏览器重定向到 Azure AD OAuth2.0 授权终结点或 OpenID Connect userinfo 终结点。这些终结点允许应用程序通过检查 id\_token 来获取有关新用户的信息。

或者，你的 Web 应用程序还可能会提供允许管理员“注册我的公司”的体验。这种体验也会将用户重定向到 Azure AD OAuth 2.0 授权终结点。在此情况下，你也可以传递一个 prompt=admin\_consent 参数来触发管理员同意体验，此时管理员会代表其组织授予许可。成功同意后，响应中将包含 admin\_consent=true。当换取访问令牌时，你还会收到一个 id\_token，其中提供了有关注册你的应用程序的组织和管理员的信息。

#### 为单页面应用程序启用 OAuth 2.0 隐式授权

通常将单页面应用程序 (SPA) 构建为一个在浏览器中运行的 JavaScript 重型前端，该前端调用应用程序的 Web API 后端来执行其业务逻辑。对于托管在 Azure AD 中的 SPA，可以使用 OAuth 2.0 隐式授权对具有 Azure AD 的用户进行身份验证，并获取可用来保护从应用程序 JavaScript 客户端到其后端 web API 的调用的令牌。用户授予同意之后，可以使用同一个身份验证协议来获取令牌以保护客户端与针对应用程序配置的其他 Web API 资源之间的调用。默认情况下，为应用程序禁用了 OAuth 2.0 隐式授权。你也可以通过在[应用程序清单](active-directory-application-manifest.md)（表示应用程序标识配置的 JSON 文件）中设置 `oauth2AllowImplicitFlow`”` 值，来为应用程序启用 OAuth 2.0 隐式授权。

##### 启用 OAuth 2.0 隐式授权

1. 登录到 Azure 管理门户。
1. 在左侧菜单中单击“Active Directory”图标，然后单击所需的目录。
1. 在顶部菜单中，单击“应用程序”，然后单击要配置的应用程序。“快速启动”页将显示单一登录信息和其他配置信息。
1. 在命令栏中单击“管理清单”按钮，然后选择“下载清单”。打开 JSON 应用程序清单文件并将 "oauth2AllowImplicitFlow" 值设为“true”。默认情况下，它是“false”。

       "oauth2AllowImplicitFlow": true,

1. 保存更新的 JSON 文件，然后将其上载，方法是单击命令栏中的“管理清单”按钮，选择“上载清单”，浏览到更新的清单文件，然后将其选中。上载后，你的 Web API 即已配置为使用 OAuth 2.0 隐式授权来对用户进行身份验证。


### 授予访问权限的旧体验

本部分介绍 2014 年 3 月 12 日以前的旧同意体验。这种体验仍受支持，如下所述。在新功能推出之前，你只能授予以下权限：

- 让用户从其组织登录

- 让用户登录，并读取其组织的目录数据（仅限应用程序）

- 让用户登录，并读取和写入其组织的目录数据（仅限应用程序）

你可以遵循[利用 Azure AD 开发多租户 Web 应用程序](https://msdn.microsoft.com/library/azure/dn151789.aspx)中的步骤来授予对 Azure AD 中注册的新应用程序的访问权限。必须知道，新同意框架允许的强大应用程序要多得多，并且还允许用户同意这些应用程序，而不仅限于管理员。

#### 生成为外部用户授予访问权限的链接（旧）

为了让外部用户使用其组织帐户注册你的应用程序，你需要更新你的应用程序，以显示一个可以链接到 Azure AD 上的页面的按钮，使他们能够授予访问权限。[适用于集成应用程序的品牌准则](active-directory-branding-guidelines.md)主题中介绍了有关此注册按钮的品牌指导。在用户授予或拒绝访问权限后，Azure AD 访问授权页会将浏览器重定向回你的应用程序并返回响应。有关应用程序属性的详细信息，请参阅[应用程序对象和服务主体](active-directory-application-objects.md)。

访问授权页由 Azure AD 创建，你可以在管理门户中应用程序的“配置”页上找到访问授权页的链接。若要转到“配置”页，请单击 Azure AD 租户的顶部菜单中的“应用程序”链接，单击要配置的应用程序，然后在“快速启动”页的顶部菜单中单击“配置”。

应用程序的链接如下所示：`http://account.activedirectory.windowsazure.com/Consent.aspx?ClientID=058eb9b2-4f49-4850-9b78-469e3176e247&RequestedPermissions=DirectoryReaders&ConsentReturnURL=https%3A%2F%2Fadatum.com%2FExpenseReport.aspx%3FContextId%3D123456`。下表描述了该链接的组成部分：

|参数|说明|
|---|---|
|ClientId|必需。在添加应用程序的过程中获取的客户端 ID。|
|RequestedPermissions|可选。应用程序正在请求的访问级别，此值将向授予应用程序访问权限的用户显示。如果未指定，则请求的访问级别将默认为仅限单一登录。其他选项为 DirectoryReaders 和 DirectoryWriters。有关这些访问级别的详细信息，请参阅“应用程序访问级别”。|
|ConsentReturnUrl|可选。要将访问授权响应返回到的 URL。此值必须采用 URL 编码，并且必须与应用程序定义中配置的回复 URL 位于同一个域中。如果未提供此值，则会将访问授权响应重定向到你配置的回复 URL。|

指定独立于回复 URL 的 ConsentReturnUrl 可让应用实施独立的逻辑，该逻辑可从回复 URL 处理不同 URL 上的响应（通常会处理用于登录的 SAML 令牌）。你还可以在 ConsentReturnURL 编码的 URL 中指定其他参数；在重定向时，会将这些参数作为查询字符串参数传回给你的应用程序。使用此机制可以维护更多信息，或者将应用程序发出的访问授权请求绑定到来自 Azure AD 的响应。

#### 授予访问权限用户体验和响应（旧）

以下示意图显示了当应用程序重定向到访问授权链接时的用户体验。

如果用户尚未登录，系统会提示他们执行以下操作：

![登录到 AAD](./media/active-directory-integrating-applications/signintoaad.png)

用户进行身份验证后，Azure AD 会将用户重定向到访问授权页：

![授予访问权限](./media/active-directory-integrating-applications/grantaccess.png)

>[AZURE.NOTE]只有外部组织的公司管理员才能授予对你应用程序的访问权限。如果用户不是公司管理员，系统会向他们提供相应的选项，用于向其公司管理员发送邮件以请求授予对此应用程序的访问权限。

客户通过单击“授予访问权限”授予对你应用程序的访问权限或者通过单击“取消”拒绝访问后，Azure AD 将向 ConsentReturnUrl 或你配置的回复 URL 发送响应。此响应包含以下参数：

|参数|说明|
|---|---|
|TenantId|Azure AD 中已授予对你应用的访问权限的组织的唯一 ID。仅当客户授予了访问权限时，才要指定此参数。|
|Consent|如果已授予对应用程序的访问权限，则该值将设置为 Granted；如果请求被拒绝，则设置为 Denied。|

如果指定了其他参数作为 ConsentReturnUrl 编码 URL 的一部分，则会将这些参数返回到应用。下面显示了对某个访问授权请求返回的示例响应，该响应指明应用程序已被授权，并包含访问授权请求中提供的 ContextID：`https://adatum.com/ExpenseReport.aspx?ContextID=123456&Consent=Granted&TenantId=f03dcba3-d693-47ad-9983-011650e64134`。

>[AZURE.NOTE]访问授权响应不包含用户的安全令牌；应用必须单独将用户登录。

下面显示了对某个已被拒绝的访问授权请求返回的示例响应：`https://adatum.com/ExpenseReport.aspx?ContextID=123456&Consent=Denied`

#### 滚动更新应用程序密钥以实现对图形 API 的无中断访问（旧版）

在应用的整个生存期内，你可能需要更改在调用 Azure AD 以获取用于调用图形 API 的访问令牌时使用的密钥。通常，密钥更改操作划分为两种类别：密钥已泄漏时进行的紧急滚动更新，以及当前密钥即将过期时进行的滚动更新。在刷新密钥时，应遵循以下过程来为应用提供无中断访问（主要适用于第二种情况）。

1. 在 Azure 管理门户中，单击你的目录租户，从顶部菜单中单击“应用程序”，然后单击你要配置的应用。“快速启动”页将显示单一登录信息和其他配置信息。

1. 在顶部菜单中单击“配置”以查看应用的属性列表，同时你将会看到密钥列表。

1. 在“密钥”下，单击显示“选择持续时间”的下拉列表并选择 1 年或 2 年，然后单击命令栏中的“保存”。这将生成应用的新密码密钥。复制此新密码密钥。此时，你的应用可以同时使用现有密钥和新密钥从 Azure AD 中获取访问令牌。

1. 返回到你的应用并更新配置，以开始使用新的密码密钥。有关要在何处执行此项更新的示例，请参阅[使用图形 API 查询 Azure AD](https://msdn.microsoft.com/library/azure/dn151791.aspx)。

1. 现在，你应该在整个生产环境中应用此更改 – 首先在一个节点上验证更改，然后将它应用到环境中的其他节点。

1. 在整个生产部署中完成更新后，你随时可以返回 Azure 管理门户并删除旧密钥。

#### 启用访问权限后更改应用属性（旧）

为外部用户启用对你的应用的访问权限后，仍可以继续在 Azure 管理门户中对应用的属性进行更改。但是，在进行应用更改before，已授予对你应用的访问权限的客户在 Azure 管理门户中查看有关该应用程序的详细信息时，将看不到这些更改。将应用提供给客户使用后，在进行某些更改时，你必须保持谨慎。例如，如果你更新了 App ID URI，则在进行此项更改之前授予了访问权限的现有客户将无法使用其公司帐户或学校帐户登录你的应用。

如果更改 RequestedPermissions 以请求更高的访问级别，并且现有客户使用的应用功能现在通过这个更高的访问级别利用新的 API 调用，那么，这些客户可能会收到图形 API 发来的访问被拒绝响应。你的应用应该处理这种情况，并要求客户使用更高访问级别请求授予对应用的访问权限。

## 删除应用程序

本部分介绍如何从目录中删除单租户和多租户应用程序。

### 从目录中删除单租户应用程序

1. 登录到 Azure 管理门户。

1. 在左侧菜单中单击“Active Directory”图标，然后单击所需的目录。

1. 在顶部菜单中，单击“应用程序”，然后单击要配置的应用程序。“快速启动”页将显示单一登录信息和其他配置信息。

1. 单击命令栏中的“删除”按钮。

1. 在确认消息中单击“是”。

### 从目录中删除多租户应用程序

1. 登录到 Azure 管理门户。

1. 在左侧菜单中单击“Active Directory”图标，然后单击所需的目录。

1. 在顶部菜单中，单击“应用程序”，然后单击要配置的应用程序。“快速启动”页将显示单一登录信息和其他配置信息。

1. 在“应用程序是多租户的”部分中，单击“否”。这会将你的应用程序转换为单租户应用程序，但该应用程序仍保留在已同意它的组织中。

1. 单击命令栏中的“删除”按钮。

1. 在确认消息中单击“是”。

公司管理员若要删除应用程序对其目录的访问权限（在授予许可后），必须使用一个 Azure 订阅来通过 Azure 管理门户删除访问权限。公司管理员也可以使用 [Azure AD PowerShell Cmdlet](http://go.microsoft.com/fwlink/?LinkId=294151) 删除访问权限。

## 后续步骤

- 请参阅[适用于集成应用的品牌准则](active-directory-branding-guidelines)

- 了解[应用程序对象和服务主体对象](active-directory-application-objects)

- 了解 [Azure Active Directory 应用程序清单](active-directory-application-manifest)

- 访问 [Active Directory 开发人员指南](active-directory-developer's guide)

<!---HONumber=Mooncake_0118_2016-->