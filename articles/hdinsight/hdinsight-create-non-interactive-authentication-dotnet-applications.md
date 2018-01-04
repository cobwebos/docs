---
title: "在 Azure HDInsight 中创建非交互式身份验证 .NET 应用程序 | Microsoft Docs"
description: "了解如何在 Azure HDInsight 中创建非交互式身份验证 Microsoft .NET 应用程序。"
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 8e32430f-6404-498a-9fcd-f20338d964af
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: b2b24747ce4ea8499c999c693f00fb09178d52b0
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2018
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>创建非交互式身份验证 .NET HDInsight 应用程序
可以在应用程序自身的标识（非交互式）或应用程序的已登录用户标识（交互式）下运行 Microsoft .NET Azure HDInsight 应用程序。 本文介绍了如何创建非交互式身份验证 .NET 应用程序以连接到 Azure 并管理 HDInsight。 有关交互式应用程序的示例，请参阅[连接到 Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight)。 

需要从非交互式 .NET 应用程序中获取：

* Azure 订阅租户 ID（也称为目录 ID）。 请参阅[获取租户 ID](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id)。
* Azure Active Directory (Azure AD) 应用程序客户端 ID。 请参阅[创建 Azure Active Directory 应用程序](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)和[获取应用程序 ID](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)。
* Azure AD 应用程序密钥。 请参阅[获取应用程序身份验证密钥](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)。

## <a name="prerequisites"></a>先决条件
* HDInsight 群集。 请参阅[入门教程](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)。

## <a name="assign-a-role-to-the-azure-ad-application"></a>将角色分配给 Azure AD 应用程序
向 Azure AD 应用程序分配[角色](../active-directory/role-based-access-built-in-roles.md)，为其授予执行操作的权限。 可将作用域设置为订阅、资源组或资源级别。 较低级别的作用域将继承权限。 （例如，将某个应用程序添加到资源组的“读取者”角色意味着该应用程序可以读取该资源组及其中包含的所有资源。）在本教程中，将在资源组级别设置作用域。 有关详细信息，请参阅[使用角色分配管理对 Azure 订阅资源的访问权限](../active-directory/role-based-access-control-configure.md)。

**将“所有者”角色添加到 Azure AD 应用程序**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中，选择“资源组”。
3. 选择包含 HDInsight 群集（在本教程中的后面部分，会在其中运行 Hive 查询）的资源组。 如果有大量资源组，可以使用筛选器查找所需的资源组。
4. 在“资源组”菜单中，选择“访问控制（标识和访问管理）”。
5. 在“用户”下，选择“添加”。
6. 按照说明将“所有者”角色添加到 Azure AD 应用程序。 成功添加角色后，应用程序将在“用户”下列出，含“所有者”角色。 

## <a name="develop-an-hdinsight-client-application"></a>开发 HDInsight 客户端应用程序

1. 创建 C# 控制台应用程序。
2. 添加以下 NuGet 包：

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. 运行以下代码：

    ```csharp
    using System;
    using System.Security;
    using Microsoft.Azure;
    using Microsoft.Azure.Common.Authentication;
    using Microsoft.Azure.Common.Authentication.Factories;
    using Microsoft.Azure.Common.Authentication.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.HDInsight;
    
    namespace CreateHDICluster
    {
        internal class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
    
            private static Guid SubscriptionId = new Guid("<Enter your Azure subscription ID>");
            private static string tenantID = "<Enter your tenant ID (also called directory ID)>";
            private static string applicationID = "<Enter your application ID>";
            private static string secretKey = "<Enter the application secret key>";
    
            private static void Main(string[] args)
            {
                var key = new SecureString();
                foreach (char c in secretKey) { key.AppendChar(c); }
    
                var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
    
                var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                resourceManagementClient.Providers.Register("Microsoft.HDInsight");
    
                _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
    
                var results = _hdiManagementClient.Clusters.List();
                foreach (var name in results.Clusters)
                {
                    Console.WriteLine("Cluster Name: " + name.Name);
                    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                    Console.WriteLine("\t Cluster location: " + name.Location);
                    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                }
                Console.WriteLine("Press Enter to continue");
                Console.ReadLine();
            }
    
            /// Get the access token for a service principal and provided key.          
            public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
            {
                var authFactory = new AuthenticationFactory();
                var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                var accessToken =
                    authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
    
                return new TokenCloudCredentials(accessToken);
            }
    
            public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
            {
                return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
            }
        }
    }
    ```


## <a name="next-steps"></a>后续步骤
* [在 Azure 门户中创建 Azure Active Directory 应用程序和服务主体](../azure-resource-manager/resource-group-create-service-principal-portal.md)。
* 了解如何[使用 Azure 资源管理器对服务主体进行身份验证](../azure-resource-manager/resource-group-authenticate-service-principal.md)。
* 了解 [Azure 基于角色的访问控制 (RBAC)](../active-directory/role-based-access-control-configure.md)。
