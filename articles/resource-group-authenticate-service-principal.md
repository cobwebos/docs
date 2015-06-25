<properties
   pageTitle="通过 Azure 资源管理器对服务主体进行身份验证"
   description="介绍如何通过基于角色的访问控制向服务主体授予访问权限，并对服务主体进行身份验证。演示如何使用 PowerShell 和 Azure CLI 执行这些任务。"
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.date="05/15/2015"
   wacn.date=""/>

# 通过 Azure 资源管理器对服务主体进行身份验证

本主题说明如何允许服务主体（例如自动化的过程、应用程序或服务）访问订阅中的其他资源。借助 Azure 资源管理器，可以使用基于角色的访问控制向服务主体授予允许的操作，并对该服务主体进行身份验证。本主题说明如何使用 PowerShell 和 Azure CLI 将角色分配给服务主体，并对服务主体进行身份验证。


## 概念
1. Azure Active Directory (AAD) - 云的标识与访问管理服务。有关详细信息，请参阅[什么是 Azure Active Directory](active-directory-whatis)
2. 服务主体 - 目录中需要访问其他资源的应用程序实例。
3. AD 应用程序 - 向 AAD 标识某个应用程序的目录记录。有关详细信息，请参阅 [Azure AD 中的身份验证基本知识](https://msdn.microsoft.com/zh-cn/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth)。

## 使用 PowerShell 向服务主体授予访问权限并对服务主体进行身份验证

如果你未安装 Azure PowerShell，请参阅[如何安装和配置 Azure PowerShell](powershell-install-configure)。

首先，创建一个服务主体。为此，我们必须在目录中创建一个应用程序。本部分将指导你在目录中创建新的应用程序。

1. 运行 **New-AzureADApplication** 命令创建新的 AAD 应用程序。提供应用程序的显示名称、描述应用程序的页面的 URI（该链接未验证）、标识应用程序的 URI，以及应用程序标识的密码。

        PS C:\> $azureAdApplication = New-AzureADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password "<Your_Password>"

     将返回该 Azure AD 应用程序。需要使用 **ApplicationId** 属性创建服务主体、角色分配以及获取 JWT 令牌。保存输出，或将它捕获到变量中。

        Type                    : Application
        ApplicationId           : a41acfda-d588-47c9-8166-d659a335a865
        ApplicationObjectId     : a26aaa48-bd52-4a7f-b29f-1bebf74c91e3
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access My
                              Application on behalf of the signed-in user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <<Your Application Display Name>>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId":
                            "b866ef28-9abb-4698-8c8f-eb4328533831",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application
                             to access <<Your Application Display Name>> on your behalf.",
                            "userConsentDisplayName": "Access <<Your Application Display Name>>",
                            "lang": null
                          }}


2. 创建应用程序的服务主体。

        PS C:\> New-AzureADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     现在你已在目录中创建服务主体，但未将任何权限或范围分配给服务。需要显式向服务主体授予权限，才能在某个范围执行操作。

3. 向服务主体授予对你的订阅的权限。在此示例中，你将要向服务主体授予读取订阅中所有资源的权限。对于 **ServicePrincipalName** 参数，请提供你在创建应用程序时使用的 **ApplicationId** 或 **IdentifierUris**。有关基于角色的访问控制的详细信息，请参阅[管理和审核对资源的访问权限](resource-group-rbac)

        PS C:\> New-AzureRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

4. 检索在其中创建了角色分配的订阅。稍后将使用此订阅来获取服务主体角色分配所在租户的 **TenantId**。

        PS C:\> $subscription = Get-AzureSubscription | where { $_.IsCurrent }

     如果角色分配不是在当前选择的订阅中创建的，你可以指定 **SubscriptoinId** 或 **SubscriptionName** 参数来检索其他订阅。

5. 运行 **Get-Credential** 命令，以创建包含你的凭据的新 **PSCredential** 对象。

        PS C:\> $creds = Get-Credential

     系统会提示你输入凭据。

     ![][1]

     对于用户名，请使用你在创建应用程序时所用的 **ApplicationId** 或 **IdentifierUris**。对于密码，请使用你在创建帐户时指定的密码。

6. 使用输入的凭据作为 **Add-AzureAccount** Cmdlet 的输入，以将服务主体登录：

        PS C:\> Add-AzureAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId

     现在，你应该已经作为所创建 AAD 应用程序的服务主体进行身份验证。


## 使用 Azure CLI 向服务主体授予访问权限并对服务主体进行身份验证

如果你未安装适用于 Mac、Linux 和 Windows 的 Azure CLI，请参阅[安装和配置 Azure CLI](xplat-cli-install)。

1. 运行 **azure ad app create** 命令创建新的 AAD 应用程序。提供应用程序的显示名称、描述应用程序的页面的 URI（该链接未验证）、标识应用程序的 URI，以及应用程序标识的密码。

        azure ad app create --name "<Your Application Display Name>" --home-page "<https://YourApplicationHomePage>" --identifier-uris "<https://YouApplicationUri>" --password <Your_Password>

    将返回该 Azure AD 应用程序。需要使用 ApplicationId 属性创建服务主体、角色分配以及获取 JWT 令牌。

        info:    Executing command ad app create
        + Creating application exampleapp
        data:    Application Id:          b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Application Object Id:   d5c519e2-6149-447e-b323-88d2c4ea27de
        data:    Application Permissions:  
        data:                             claimValue:  user_impersonation
        data:                             description:  Allow the application to access exampleapp on behalf of the signed-in user.
        ...
        info:    ad app create command OK

2. 创建应用程序的服务主体。提供上一步返回的应用程序 ID。

        azure ad sp create b57dd71d-036c-4840-865e-23b71d8098ec

    随后将返回新的服务主体。授权时需要使用对象 ID。

        info:    Executing command ad sp create
        + Creating service principal for application b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Object Id:               47193a0a-63e4-46bd-9bee-6a9f6f9c03cb
        data:    Display Name:            exampleapp
        ...
        info:    ad sp create command OK

    现在你已在目录中创建服务主体，但未将任何权限或范围分配给服务。需要显式向服务主体授予权限，才能在某个范围执行操作。

3. 向服务主体授予对你的订阅的权限。在此示例中，你将要向服务主体授予读取订阅中所有资源的权限。对于 **ServicePrincipalName** 参数，请提供你在创建应用程序时使用的 **ApplicationId** 或 **IdentifierUris**。有关基于角色的访问控制的详细信息，请参阅[管理和审核对资源的访问权限](resource-group-rbac)

        azure role assignment create --objectId 47193a0a-63e4-46bd-9bee-6a9f6f9c03cb -o Reader -c /subscriptions/{subscriptionId}/

4. 若要确定服务主体角色分配所在租户的 **TenantId**，请列出帐户，然后查找输出中的 **TenantId** 属性。

        azure account list

5. 使用作为你的标识的服务主体登录。对于用户名，请使用你在创建应用程序时所用的 **ApplicationId**。对于密码，请使用你在创建帐户时指定的密码。

        azure login -u "<ApplicationId>" -p "<password>" --service-principal --tenant "<TenantId>"

    现在，你应该已经作为所创建 AAD 应用程序的服务主体进行身份验证。

## 后续步骤
入门

- [Azure 资源管理器概述](resource-group-overview)  
- [将 Azure PowerShell 与 Azure 资源管理器配合使用](powershell-azure-resource-manager)
- [将适用于 Mac、Linux 和 Windows 的 Azure CLI 与 Azure 资源管理配合使用](xplat-cli-azure-resource-manager)  
- [使用 Azure 门户管理 Azure 资源](resource-group-portal)


创建和部署应用程序

- [创作 Azure 资源管理器模板](resource-group-authoring-templates)  
- [使用 Azure 资源管理器模板部署应用程序](resource-group-template-deploy)  
- [Azure 中的资源组部署疑难解答](resource-group-deploy-debug)  
- [Azure 资源管理器模板函数](resource-group-template-functions)  
- [高级模板操作](resource-group-advanced-template)  
- [使用 .NET 库和模板部署 Azure 资源](arm-template-deployment)

组织资源

- [使用标记来组织 Azure 资源](resource-group-using-tags)  

管理和审核访问权限

- [管理和审核对资源的访问权限](resource-group-rbac)  
- [使用 Azure 门户创建新的 Azure 服务主体](resource-group-create-service-principal-portal)  



<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=61-->