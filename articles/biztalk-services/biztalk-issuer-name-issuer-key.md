---
title: "BizTalk 服务中的颁发者名称和颁发者密钥 | Microsoft Docs"
description: "了解如何为 BizTalk 服务中的 Service Bus 或 Access Control (ACS) 检索颁发者名称和颁发者密钥。 MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 067fe356-d1aa-420f-b2f2-1a418686470a
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.translationtype: Human Translation
ms.sourcegitcommit: 71f9dd111ebdbe885f33d162b2ea320dfaa167bb
ms.openlocfilehash: 4fb13a158c660105a5fc8f79a92c67ba65c5356d
ms.contentlocale: zh-cn
ms.lasthandoff: 11/17/2016


---
# <a name="biztalk-services-issuer-name-and-issuer-key"></a>BizTalk 服务：颁发者名称和颁发者密钥

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Azure BizTalk 服务使用 Service Bus 颁发者名称和颁发者密钥以及 Access Control 颁发者名称和颁发者密钥。 具体而言：

| 任务 | 哪个颁发者名称和颁发者密钥 |
| --- | --- |
| 从 Visual Studio 部署应用程序 |Access Control 颁发者名称和颁发者密钥 |
| 配置 Azure BizTalk 服务门户 |Access Control 颁发者名称和颁发者密钥 |
| 在 Visual Studio 中使用 BizTalk 适配器服务创建 LOB 中继 |Service Bus 颁发者名称和颁发者密钥 |

本主题列出了检索颁发者名称和颁发者密钥的步骤。 

## <a name="access-control-issuer-name-and-issuer-key"></a>Access Control 颁发者名称和颁发者密钥
Access Control 颁发者名称和颁发者密钥由以下各项使用：

* 在 Visual Studio 中创建的 Azure BizTalk 服务应用程序：若要在 Visual Studio 中成功将 BizTalk 服务应用程序部署到 Azure，请输入访问控制颁发者名称和颁发者密钥。 
* Azure BizTalk 服务门户：创建 BizTalk 服务并打开 BizTalk 服务门户时，会使用相同的访问控制值对访问控制颁发者名称和颁发者密钥自动注册部署。

### <a name="get-the-access-control-issuer-name-and-issuer-key"></a>获取访问控制颁发者名称和颁发者密钥

若要使用 ACS 进行身份验证，并获取颁发者名称和颁发者密钥值，总体步骤包括：

1. 安装 [Azure PowerShell cmdlet](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)。
2. 添加 Azure 帐户：`Add-AzureAccount`
3. 返回订阅名称：`get-azuresubscription`
4. 选择订阅：`select-azuresubscription <name of your subscription>` 
5. 创建新的命名空间：`new-azuresbnamespace <name for the service bus> "Location" -CreateACSNamespace $true -NamespaceType Messaging`

    示例：   `new-azuresbnamespace biztalksbnamespace "South Central US" -CreateACSNamespace $true -NamespaceType Messaging`
      
5. 创建新 ACS 命名空间时（这可能需要几分钟），会在连接字符串中列出颁发者名称和颁发者密钥值： 

    ```
    Name                  : biztalksbnamespace
    Region                : South Central US
    DefaultKey            : abcdefghijklmnopqrstuvwxyz
    Status                : Active
    CreatedAt             : 10/18/2016 9:36:30 PM
    AcsManagementEndpoint : https://biztalksbnamespace-sb.accesscontrol.windows.net/
    ServiceBusEndpoint    : https://biztalksbnamespace.servicebus.windows.net/
    ConnectionString      : Endpoint=sb://biztalksbnamespace.servicebus.windows.net/;SharedSecretIssuer=owner;SharedSecretValue=abcdefghijklmnopqrstuvwxyz
    NamespaceType         : Messaging
    ```

总结：  
颁发者名称 = SharedSecretIssuer  
颁发者密钥 = SharedSecretKey

在 [New-azuresbnamespace](https://msdn.microsoft.com/library/dn495165.aspx) cmdlet 中了解详细信息。 

## <a name="service-bus-issuer-name-and-issuer-key"></a>Service Bus 颁发者名称和颁发者密钥
Service Bus 颁发者名称和颁发者密钥由 BizTalk 适配器服务使用。 在 Visual Studio 中的 BizTalk 服务项目中，使用 BizTalk 适配器服务连接到本地业务线 (LOB) 系统。 若要连接，请创建 LOB 中继并输入 LOB 系统的详细信息。 在执行此操作时，你还可以输入 Service Bus 颁发者名称和颁发者密钥。

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>检索 Service Bus 颁发者名称和颁发者密钥
1. 登录到 [Azure 经典门户](http://go.microsoft.com/fwlink/p/?LinkID=213885)。
2. 在左侧导航窗格中，选择“服务总线”。
3. 选择命名空间。 在任务栏中，选择“连接信息”。 这会显示“默认颁发者”（颁发者名称）和“默认密钥”（颁发者密钥）。 可以复制其值。  

总结：  
颁发者名称 = 默认颁发者  
颁发者密钥 = 默认密钥

## <a name="next"></a>下一步
其他 Azure BizTalk 服务主题：

* [安装 Azure BizTalk 服务 SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [教程：Azure BizTalk 服务](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [如何开始使用 Azure BizTalk 服务 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Azure BizTalk 服务](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>另请参阅
* [如何：使用 ACS 管理服务配置服务标识](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
* [BizTalk 服务：开发人员版、基本版、标准版和高级版图表](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk 服务：使用 Azure 经典门户预配](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [BizTalk 服务：预配状态图表](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [Biztalk 服务：“仪表板”、“监视”和“缩放”选项卡](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk 服务：备份和还原](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk 服务：限制](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>


