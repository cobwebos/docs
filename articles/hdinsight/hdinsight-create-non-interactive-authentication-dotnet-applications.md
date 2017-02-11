---
title: "创建非交互式身份验证 .NET HDInsight 应用程序 | Microsoft Docs"
description: "了解如何创建非交互式身份验证 .NET HDInsight 应用程序。"
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 8e32430f-6404-498a-9fcd-f20338d964af
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: cc59d7785975e3f9acd574b516d20cd782c22dac
ms.openlocfilehash: d4324ac080bc68af467652a9942fd3e97eb2d517


---
# <a name="create-non-interactive-authentication-net-hdinsight-applications"></a>创建非交互式身份验证 .NET HDInsight 应用程序
你可以在应用程序自身的标识（非交互式）或应用程序的已登录用户标识（交互式）下执行 .NET Azure HDInsight 应用程序。 有关交互式应用程序的示例，请参阅[使用 HDInsight .NET SDK 提交 Hive/Pig/Sqoop 作业](hdinsight-submit-hadoop-jobs-programmatically.md)。 本文介绍了如何创建非交互式身份验证 .NET 应用程序，以连接到 Azure HDInsight 并提交 Hive 作业。

从 .NET 应用程序，你将需要：

* Azure 订阅租户 ID
* Azure Directory 应用程序客户端 ID
* Azure Directory 应用程序密钥。  

主要过程包括以下步骤：

1. 创建 Azure Directory 应用程序。
2. 将角色分配给 AD 应用程序。
3. 开发客户端应用程序。

## <a name="prerequisites"></a>先决条件
* HDInsight 群集。 可以使用[入门教程](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster)中找到的说明进行创建。

## <a name="create-azure-directory-application"></a>创建 Azure Directory 应用程序
创建 Active Directory 应用程序时，实际上会同时创建该应用程序和一个服务主体。 可以在应用程序的标识下执行应用程序。

目前，必须使用 Azure 经典门户来创建新的 Active Directory 应用程序。 在以后的版本中，此功能将添加到 Azure 门户。 你也可以通过 Azure PowerShell 或 Azure CLI 执行这些步骤。 有关将 PowerShell 或 CLI 与服务主体一起使用的详细信息，请参阅[使用 Azure Resource Manager 对服务主体进行身份验证](../resource-group-authenticate-service-principal.md)。

**创建 Azure Directory 应用程序**

1. 登录到 [Azure 经典门户](https://manage.windowsazure.com/)。
2. 在左侧窗格中选择“Active Directory”。

   ![Azure 经典门户 Active Directory](.\\media\\hdinsight-create-non-interactive-authentication-dotnet-application\\active-directory.png)
3. 选择你要用来创建新应用程序的目录。 应为现有的一个目录。
4. 单击顶部的“应用程序”以列出现有的应用程序。
5. 单击底部的“添加”以添加新应用程序。
6. 输入“名称”，选择“Web 应用程序和/或 Web API”，然后单击“下一步”。

   ![新的 Azure Active Directory 应用程序](.\\media\\hdinsight-create-non-interactive-authentication-dotnet-application\\hdinsight-add-ad-application.png)
7. 输入“登录 URL”和“应用 ID URI”。 对于“登录 URL”，请提供用于描述应用程序的网站 URI。 将不验证网站是否存在。 对于“应用程序 ID URI”，请提供用于标识应用程序的 URI。 然后，单击“完成”。
   片刻之后即可创建应用程序。  创建应用程序后，门户将显示新的应用程序的速览页面。 不要关闭门户。

   ![新的 Azure Active Directory 应用程序属性](.\\media\\hdinsight-create-non-interactive-authentication-dotnet-application\\hdinsight-add-ad-application-properties.png)

**获取应用程序客户端 ID 和密钥**

1. 在新建的 AD 应用程序页中，单击顶部菜单的“配置”。
2. 创建“客户端 ID”的副本。 稍后在 .NET 应用程序中需要使用它。
3. 在“密钥”下，单击“选择持续时间”下拉列表，然后选择“1 年”或“2 年”。 保存配置之前，将不会显示密钥值。
4. 单击页面底部的“保存”。 出现密钥时，创建该密钥的副本。 稍后在 .NET 应用程序中需要使用它。

## <a name="assign-ad-application-to-role"></a>将 AD 应用程序分配到角色
必须将应用程序分配到某个[角色](../active-directory/role-based-access-built-in-roles.md)，以授予它执行操作的权限。 可将作用域设置为订阅、资源组或资源级别。 作用域的较低级别将继承权限（例如，将某个应用程序添加到资源组的“读取者”角色意味着该应用程序可以读取该资源组及其包含的所有资源）。 在本教程中，你将在资源组级别设置作用域。  由于 Azure 经典门户不支持资源组，所以此部分必须从 Azure 门户执行。

**将“所有者”角色添加到 AD 应用程序**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 单击左侧窗格中的“资源组”。
3. 单击包含 HDInsight 群集（在本教程中，将在其中运行 Hive 查询）的资源组。 如果有过多资源组，可以使用筛选器。
4. 在群集边栏选项卡中单击“访问”。

   ![云和闪电图标 = 快速启动](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)
5. 单击“用户”边栏选项卡上的“添加”。
6. 按照说明，将“所有者”角色添加到上一个过程中创建的 AD 应用程序。 成功完成后，应该可在具有“所有者”角色的“用户”边栏选项卡中看到列出的应用程序。

## <a name="develop-hdinsight-client-application"></a>开发 HDInsight 客户端应用程序
按照[在 HDInsight 中提交 Hadoop 作业](hdinsight-submit-hadoop-jobs-programmatically.md)中找到的说明，创建 C# .net 控制台应用程序。 然后，将 GetTokenCloudCredentials 方法替换为：

    public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
    {
        var authFactory = new AuthenticationFactory();

        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };

        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

        var accessToken =
            authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never)
                .AccessToken;

        return new TokenCloudCredentials(accessToken);
    }

通过 PowerShell 来检索租户 ID：

    Get-AzureRmSubscription

也可以使用 Azure CLI：

    azure account show --json


## <a name="see-also"></a>另请参阅
* [在 HDInsight 中提交 Hadoop 作业](hdinsight-submit-hadoop-jobs-programmatically.md)
* [使用门户创建 Active Directory 应用程序和服务主体](../resource-group-create-service-principal-portal.md)
* [通过 Azure Resource Manager 对服务主体进行身份验证](../resource-group-authenticate-service-principal.md)
* [Azure 基于角色的访问控制](../active-directory/role-based-access-control-configure.md)



<!--HONumber=Nov16_HO3-->


