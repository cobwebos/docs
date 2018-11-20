---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: ab9d059ede2464318205a90b8ac738727e8d89a4
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164724"
---
## <a name="obtain-an-azure-resource-manager-token"></a>获取 Azure 资源管理器令牌
Azure Active Directory 必须使用 Azure 资源管理器来验证所有针对资源执行的任务。 此处显示的示例使用密码身份验证，有关其他方法，请参阅[对 Azure 资源管理器请求进行身份验证][lnk-authenticate-arm]。

1. 将以下代码添加到 Program.cs 中的 **Main** 方法，以使用应用程序 ID 和密码从 Azure AD 中检索令牌。
   
    ```
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.microsoftonline.com/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
   
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```
2. 创建一个 **ResourceManagementClient** 对象，该对象通过在 **Main** 方法的末尾添加以下代码来使用令牌：
   
    ```
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. 创建或获取对正在使用的资源组的引用：
   
    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx