---
title: "敏感数据 - Microsoft 威胁建模工具 - Azure | Microsoft 文档"
description: "针对威胁建模工具中暴露的威胁采取的缓解措施"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 21d1ba02052862e16ef27ec313d53cd0bffcc21a
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="security-frame-sensitive-data--mitigations"></a>安全框架：敏感数据 | 缓解措施 
| 产品/服务 | 文章 |
| --------------- | ------- |
| **计算机信任边界** | <ul><li>[如果二进制文件包含敏感信息，请确保将其模糊处理](#binaries-info)</li><li>[考虑使用加密文件系统 (EFS) 来保护用户特定的机密数据](#efs-user)</li><li>[确保应用程序在文件系统中存储的敏感数据经过加密](#filesystem)</li></ul> | 
| **Web 应用程序** | <ul><li>[确保不要在浏览器中缓存敏感内容](#cache-browser)</li><li>[加密 Web 应用配置文件中包含敏感数据的部分](#encrypt-data)</li><li>[在敏感窗体和输入中显式禁用自动填充 HTML 特性](#autocomplete-input)</li><li>[确保用户屏幕上显示的敏感数据经过屏蔽](#data-mask)</li></ul> | 
| **数据库** | <ul><li>[实施动态数据屏蔽，限制透露给非特权用户的敏感数据](#dynamic-users)</li><li>[确保以加盐哈希格式存储密码](#salted-hash)</li><li>[确保数据库列中的敏感数据经过加密](#db-encrypted)</li><li>[确保启用数据库级加密 (TDE)](#tde-enabled)</li><li>[确保数据库备份经过加密](#backup)</li></ul> | 
| **Web API** | <ul><li>[确保不要在浏览器的存储中存储与 Web API 相关的敏感数据](#api-browser)</li></ul> | 
| Azure Document DB | <ul><li>[加密 DocumentDB 中存储的敏感数据](#encrypt-docdb)</li></ul> | 
| **Azure IaaS VM 信任边界** | <ul><li>[使用 Azure 磁盘加密来加密虚拟机所用的磁盘](#disk-vm)</li></ul> | 
| **Service Fabric 信任边界** | <ul><li>[加密 Service Fabric 应用程序中的机密](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[根据需要执行安全建模并使用业务单位/团队](#modeling-teams)</li><li>[尽量避免访问关键实体的共享功能](#entities)</li><li>[为用户提供有关 Dynamics CRM 共享功能的风险与良好安全做法的培训](#good-practices)</li><li>[制定开发标准规则，禁止显示异常管理中的配置详细信息](#exception-mgmt)</li></ul> | 
| **Azure 存储** | <ul><li>[使用静态数据的 Azure 存储服务加密 (SSE)（预览版）](#sse-preview)</li><li>[使用客户端加密在 Azure 存储中存储敏感数据](#client-storage)</li></ul> | 
| **移动客户端** | <ul><li>[加密写入到手机本地存储的敏感或 PII 数据](#pii-phones)</li><li>[向最终用户分发生成的二进制文件之前将它模糊处理](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[将 clientCredentialType 设置为 Certificate 或 Windows](#cert)</li><li>[WCF - 安全模式未启用](#security)</li></ul> | 

## <a id="binaries-info"></a>如果二进制文件包含敏感信息，请确保将其模糊处理

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 计算机信任边界 | 
| **SDL 阶段**               | 部署 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | 如果二进制文件包含商业机密、不得反向工程的敏感业务逻辑等敏感信息，请确保模糊处理这些文件。 这是为了阻止对程序集进行反向工程。 可以使用 `CryptoObfuscator` 等工具实现此目的。 |

## <a id="efs-user"></a>考虑使用加密文件系统 (EFS) 来保护用户特定的机密数据

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 计算机信任边界 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | 考虑使用加密文件系统 (EFS) 来保护用户特定的机密数据，防止攻击者物理访问计算机。 |

## <a id="filesystem"></a>确保应用程序在文件系统中存储的敏感数据经过加密

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 计算机信任边界 | 
| **SDL 阶段**               | 部署 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | 如果无法强制 EFS，请确保应用程序在文件系统中存储的敏感数据经过加密（例如，使用 DPAPI） |

## <a id="cache-browser"></a>确保不要在浏览器中缓存敏感内容

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型、Web 窗体、MVC5、MVC6 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | 浏览器可能会出于缓存和历史记录的目的存储信息。 这些缓存的文件存储在某个文件夹中，例如，如果使用的是 Internet Explorer，该文件夹为“Internet 临时文件”。 再次浏览这些页面时，浏览器会从缓存显示这些页面。 如果页面中显示了用户的敏感信息（例如，他们的地址、信用卡详细信息、身份证号或用户名），这些信息可能会存储在浏览器的缓存中，通过查看浏览器的缓存或者只需按下浏览器中的“后退”按钮即可检索到。 请将所有页面的 cache-control 响应标头值设置为“no-store”。 |

### <a name="example"></a>示例
```XML
<configuration>
  <system.webServer>
   <httpProtocol>
    <customHeaders>
        <add name="Cache-Control" value="no-cache" />
        <add name="Pragma" value="no-cache" />
        <add name="Expires" value="-1" />
    </customHeaders>
  </httpProtocol>
 </system.webServer>
</configuration>
```

### <a name="example"></a>示例
这可以通过筛选器来实现。 可以使用以下示例： 
```C#
public override void OnActionExecuting(ActionExecutingContext filterContext)
        {
            if (filterContext == null || (filterContext.HttpContext != null && filterContext.HttpContext.Response != null && filterContext.HttpContext.Response.IsRequestBeingRedirected))
            {
                //// Since this is MVC pipeline, this should never be null.
                return;
            }

            var attributes = filterContext.ActionDescriptor.GetCustomAttributes(typeof(System.Web.Mvc.OutputCacheAttribute), false);
            if (attributes == null || **Attributes**.Count() == 0)
            {
                filterContext.HttpContext.Response.Cache.SetNoStore();
                filterContext.HttpContext.Response.Cache.SetCacheability(HttpCacheability.NoCache);
                filterContext.HttpContext.Response.Cache.SetExpires(DateTime.UtcNow.AddHours(-1));
                if (!filterContext.IsChildAction)
                {
                    filterContext.HttpContext.Response.AppendHeader("Pragma", "no-cache");
                }
            }

            base.OnActionExecuting(filterContext);
        }
``` 

## <a id="encrypt-data"></a>加密 Web 应用配置文件中包含敏感数据的部分

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | [如何：使用 DPAPI 加密 ASP.NET 2.0 中的配置部分](https://msdn.microsoft.com/library/ff647398.aspx)、[指定受保护的配置提供程序](https://msdn.microsoft.com/library/68ze1hb2.aspx)、[使用 Azure Key Vault 保护应用程序机密](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **步骤** | Web.config、appsettings.json 等配置文件通常用于保存敏感信息，包括用户名、密码、数据库连接字符串和加密密钥。 如果不保护此类信息，攻击者或恶意用户可能会利用应用程序的漏洞来获取敏感信息，例如帐户用户名和密码、数据库名称和服务器名称。 请根据部署类型 (azure/on-prem)，使用 DPAPI 或 Azure Key Vault 等服务来加密配置文件的敏感部分。 |

## <a id="autocomplete-input"></a>在敏感窗体和输入中显式禁用自动完成 HTML 属性

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | [MSDN：自动完成特性](http://msdn.microsoft.com/library/ms533486(VS.85).aspx)、[在 HTML 中使用自动完成](http://msdn.microsoft.com/library/ms533032.aspx)、[HTML 净化漏洞](http://technet.microsoft.com/security/bulletin/MS10-071)、[又是自动完成？](http://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **步骤** | 自动完成特性指定是要打开还是关闭窗体的自动填充。 如果打开自动完成，浏览器会根据用户以前输入的值自动填充值。 例如，如果在窗体中输入新名称和密码，然后提交该窗体，则浏览器会提示是否应保存该密码。此后显示该窗体时，该名称和密码会自动填充，或者在输入名称时自动填充。 拥有本地访问权限的攻击者可能会通过浏览器缓存获取明文密码。 自动完成默认已启用，必须显式将它禁用。 |

### <a name="example"></a>示例
```C#
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a id="data-mask"></a>确保用户屏幕上显示的敏感数据经过屏蔽

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | 在屏幕上显示密码、信用卡号、身份证号等敏感数据时，应该将它们屏蔽。 这是为了防止未经授权的人员访问这些数据（例如，使用肩窥技术查看密码，或者支持人员查看用户的身份证号）。 确保不要以明文显示这些数据元素，并将其适当地屏蔽。 接受使用这些数据作为输入（例如， input type="password"）以及在屏幕上显示这些数据时，必须多加小心（例如，仅显示信用卡号的最后 4 位数）。 |

## <a id="dynamic-users"></a>实施动态数据屏蔽，限制透露给非特权用户的敏感数据

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 数据库 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | SQL Azure、OnPrem |
| **属性**              | SQL 版本 - V12，SQL 版本 - MsSQL2016 |
| **参考**              | [动态数据掩码](https://msdn.microsoft.com/library/mt130841) |
| **步骤** | 动态数据屏蔽的用途是限制透露敏感数据，防止没有相应访问权限的用户查看这些数据。 动态数据屏蔽并不旨在防止数据库用户直接连接到数据库，以及运行可以公开敏感数据片段的穷举查询。 动态数据屏蔽是其他 SQL Server 安全功能（审核、加密、行级别安全性…）的补充。强烈建议将这些功能与动态数据屏蔽结合使用，以便更好地保护数据库中的敏感数据。 请注意，此功能仅受 SQL Server 2016 及更高版本以及 Azure SQL 数据库的支持。 |

## <a id="salted-hash"></a>确保以加盐哈希格式存储密码

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 数据库 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | [使用 .NET 加密 API 的密码哈希](http://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **步骤** | 不应将密码存储在自定义用户存储数据库中。 应该改为使用 salt 值存储密码哈希。 确保用户的 salt 始终唯一，并在存储密码之前应用 b-crypt、s-crypt 或 PBKDF2，使用 150,000 次循环的最小工作系数迭代计数，消除暴力破解的可能性。| 

## <a id="db-encrypted"></a>确保数据库列中的敏感数据经过加密

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 数据库 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | SQL 版本 - 所有 |
| **参考**              | [加密 SQL Server 中的敏感数据](https://technet.microsoft.com/library/ff848751(v=sql.105).aspx)、[如何：加密 SQL Server 中的数据列](https://msdn.microsoft.com/library/ms179331)、[使用证书加密](https://msdn.microsoft.com/library/ms188061) |
| **步骤** | 信用卡号等敏感数据必须在数据库中加密。 可以使用列级加密，或者使用加密函数通过应用程序函数将数据加密。 |

## <a id="tde-enabled"></a>确保启用数据库级加密 (TDE)

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 数据库 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | [了解 SQL Server 透明数据加密 (TDE)](https://technet.microsoft.com/library/bb934049(v=sql.105).aspx) |
| **步骤** | SQL Server 中的透明数据加密 (TDE) 功能可帮助加密数据库中的敏感数据，并使用证书保护用于加密数据的密钥。 这样就可以防止没有密钥的人使用这些数据。 TDE 保护“静态数据”，包括数据文件和日志文件。 使用 TDE 能够符合各个行业制定的许多法律、法规和准则。 |

## <a id="backup"></a>确保数据库备份经过加密

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 数据库 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | SQL Azure、OnPrem |
| **属性**              | SQL 版本 - V12，SQL 版本 - MsSQL2014 |
| **参考**              | [SQL 数据库备份加密](https://msdn.microsoft.com/library/dn449489) |
| **步骤** | SQL Server 能够在创建备份时加密数据。 可以通过在创建备份指定加密算法和加密器（证书或非对称密钥），创建加密的备份文件。 |

## <a id="api-browser"></a>确保不要在浏览器的存储中存储与 Web API 相关的敏感数据

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web API | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | MVC 5、MVC 6 |
| **属性**              | 标识提供者 - ADFS，标识提供者 - Azure AD |
| **参考**              | 不适用  |
| **步骤** | <p>在某些实现中，与 Web API 身份验证相关的敏感项目存储在浏览器的本地存储中。 例如，adal.idtoken、adal.nonce.idtoken、adal.access.token.key、adal.token.keys、adal.state.login、adal.session.state、adal.expiration.key 等 Azure AD 身份验证项目。</p><p>即使注销或关闭浏览器，所有这些项目也仍会保留。 如果攻击者获取了对这些项目的访问权限，他们可以重复使用这些项目来访问受保护的资源 (API)。 确保不要在浏览器的存储中存储所有与 Web API 相关的敏感项目 如果不可避免地要使用客户端存储（例如，利用隐式 OpenIdConnect/OAuth 流的单页应用程序 (SPA) 需要在本地存储访问令牌），请使用不会持久保存数据的存储选项。 例如，优先使用 SessionStorage 而不是 LocalStorage。</p>| 

### <a name="example"></a>示例
以下 JavaScript 代码片段摘自某个自定义身份验证库，它会将身份验证项目存储在本地存储中。 应避免使用此类实现。 
```javascript
ns.AuthHelper.Authenticate = function () {
window.config = {
instance: 'https://login.microsoftonline.com/',
tenant: ns.Configurations.Tenant,
clientId: ns.Configurations.AADApplicationClientID,
postLogoutRedirectUri: window.location.origin,
cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
};
```

## <a id="encrypt-docdb"></a>加密 Cosmos DB 中存储的敏感数据

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Azure Document DB | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | 在文档数据库中存储敏感数据之前在应用程序级别将其加密，或者将敏感数据存储在 Azure 存储或 Azure SQL 等其他存储解决方案中| 

## <a id="disk-vm"></a>使用 Azure 磁盘加密来加密虚拟机所用的磁盘

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Azure IaaS VM 信任边界 | 
| **SDL 阶段**               | 部署 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | [使用 Azure 磁盘加密来加密虚拟机所用的磁盘](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) |
| **步骤** | <p>Azure 磁盘加密是一项新功能，目前以预览版提供。 此功能允许加密 IaaS 虚拟机使用的 OS 磁盘和数据磁盘。 对于 Windows，驱动器是使用行业标准 BitLocker 加密技术加密的。 对于 Linux，磁盘是使用 DM-Crypt 技术加密的。 它与 Azure 密钥保管库集成，可用于控制和管理磁盘加密密钥。 Azure 磁盘加密解决方案支持以下三种客户加密方案：</p><ul><li>在通过客户加密的 VHD 文件和客户提供的加密密钥（存储在 Azure 密钥保管库中）创建的新 IaaS VM 上启用加密。</li><li>在通过 Azure 应用商店创建的新 IaaS VM 上启用加密。</li><li>在 Azure 中已运行的现有 IaaS VM 上启用加密。</li></ul>| 

## <a id="fabric-apps"></a>加密 Service Fabric 应用程序中的机密

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Service Fabric 信任边界 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 环境 - Azure |
| **参考**              | [管理 Service Fabric 应用程序中的机密](https://azure.microsoft.com/documentation/articles/service-fabric-application-secret-management/) |
| **步骤** | 机密可以是任何敏感信息，例如存储连接字符串、密码或其他不应以明文形式处理的值。 使用 Azure Key Vault 管理 Service Fabric 应用程序中的密钥和机密。 |

## <a id="modeling-teams"></a>根据需要执行安全建模并使用业务单位/团队

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Dynamics CRM | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | 根据需要执行安全建模并使用业务单位/团队 |

## <a id="entities"></a>尽量避免访问关键实体的共享功能

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Dynamics CRM | 
| **SDL 阶段**               | 部署 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | 尽量避免访问关键实体的共享功能 |

## <a id="good-practices"></a>为用户提供有关 Dynamics CRM 共享功能的风险与良好安全做法的培训

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Dynamics CRM | 
| **SDL 阶段**               | 部署 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | 为用户提供有关 Dynamics CRM 共享功能的风险与良好安全做法的培训 |

## <a id="exception-mgmt"></a>制定开发标准规则，禁止显示异常管理中的配置详细信息

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Dynamics CRM | 
| **SDL 阶段**               | 部署 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | 制定开发标准规则，禁止在开发环境外部显示异常管理中的配置详细信息。 在代码评审或定期检查过程中测试此规则。|

## <a id="sse-preview"></a>使用静态数据的 Azure 存储服务加密 (SSE)（预览版）

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Azure 存储空间 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | StorageType - Blob |
| **参考**              | [静态数据的 Azure 存储服务加密（预览版）](https://azure.microsoft.com/documentation/articles/storage-service-encryption/) |
| **步骤** | <p>静态数据的 Azure 存储服务加密 (SSE) 可帮助你保护数据，使你的组织能够信守在安全性与合规性方面所做的承诺。 使用此功能，Azure 存储空间可以先自动加密数据，再将数据保存到存储空间，并在检索之前解密数据。 加密、解密和密钥管理对于用户而言是完全透明的。 SSE 仅适用于块 Blob、页 Blob 以及追加 Blob。 其他类型的数据（包括表、队列和文件）将不会加密。</p><p>加密和解密工作流：</p><ul><li>客户对存储帐户启用加密</li><li>当客户将新数据（PUT Blob、PUT 块、PUT 页等等）写入 Blob 存储时，每个写入将使用 256 位 AES 加密（可用的最强块加密法之一）进行加密</li><li>当客户需要访问数据（GET Blob 等）时，数据会在返回给用户之前自动解密</li><li>如果已禁用加密，不会再加密新的写入，现有加密数据在用户重新写入之前将保持加密。 启用加密时，向 Blob 存储的写入会加密。 数据状态在用户启用/禁用存储帐户的加密之间切换时不会更改</li><li>所有加密密钥由 Microsoft 存储、加密和管理</li></ul><p>请注意，用于加密的密钥目前由 Microsoft 管理。 Microsoft 最初将生成密钥，管理密钥的安全存储，并根据 Microsoft 内部策略的定义定期轮转密钥。 将来，客户能够管理自己的加密密钥，并提供从 Microsoft 管理的密钥到客户管理的密钥的迁移路径。</p>| 

## <a id="client-storage"></a>使用客户端加密在 Azure 存储中存储敏感数据

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Azure 存储空间 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | [Microsoft Azure 存储的客户端加密和 Azure Key Vault](https://azure.microsoft.com/documentation/articles/storage-client-side-encryption/)、[教程：在 Microsoft Azure 存储中使用 Azure Key Vault 加密和解密 Blob](https://azure.microsoft.com/documentation/articles/storage-encrypt-decrypt-blobs-key-vault/)、[使用 Azure 加密扩展在 Azure Blob 存储中安全存储数据](https://blogs.msdn.microsoft.com/partnercatalystteam/2015/06/17/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions/) |
| **步骤** | <p>用于 .NET 的 Azure 存储客户端库 Nuget 包支持在上传到 Azure 存储之前加密客户端应用程序中的数据，并在下载到客户端时解密数据。 此库还支持与 Azure 密钥保管库集成，以便管理存储帐户密钥。 下面是客户端加密的工作原理的简要说明：</p><ul><li>Azure 存储客户端 SDK 生成内容加密密钥 (CEK)，这是一次性使用的对称密钥</li><li>可使用此 CEK 将客户数据加密</li><li>然后，使用密钥加密密钥 (KEK) 对此 CEK 进行包装（加密）。 KEK 由密钥标识符标识，可以是非对称密钥对或对称密钥，还可以在本地托管或存储在 Azure 密钥保管库中。 存储空间客户端本身永远无法访问 KEK。 它只能调用密钥保管库提供的密钥包装算法。 客户可根据需要选择使用自定义提供程序进行密钥包装/解包</li><li>然后，将已加密的数据上传到 Azure 存储服务。 请参阅参考部分中的链接，了解低级别实施详细信息。</li></ul>|

## <a id="pii-phones"></a>加密写入到手机本地存储的敏感或 PII 数据

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 移动客户端 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 通用、Xamarin  |
| **属性**              | 不适用  |
| **参考**              | [使用 Microsoft Intune 策略管理设备上的设置和功能](https://docs.microsoft.com/intune/deploy-use/manage-settings-and-features-on-your-devices-with-microsoft-intune-policies#create-a-configuration-policy)、[Keychain Valet](https://components.xamarin.com/view/square.valet) |
| **步骤** | <p>如果应用程序在手机文件系统中写入用户 PII 等敏感信息（电子邮件、电话号码、名字、姓氏、首选项等），则在写入本地文件系统之前应将这些信息加密。 如果应用程序是企业应用程序，请使用 Windows Intune 来探索发布应用程序的可能性。</p>|

### <a name="example"></a>示例
可以使用以下安全策略来配置 Intune，以保护敏感数据： 
```C#
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>示例
如果应用程序不是企业应用程序，请使用平台提供的密钥存储和密钥链来存储加密密钥，这样就可以在文件系统中执行加密操作。 以下代码片段演示如何使用 xamarin 访问密钥链中的密钥： 
```C#
        protected static string EncryptionKey
        {
            get
            {
                if (String.IsNullOrEmpty(_Key))
                {
                    var query = new SecRecord(SecKind.GenericPassword);
                    query.Service = NSBundle.MainBundle.BundleIdentifier;
                    query.Account = "UniqueID";

                    NSData uniqueId = SecKeyChain.QueryAsData(query);
                    if (uniqueId == null)
                    {
                        query.ValueData = NSData.FromString(System.Guid.NewGuid().ToString());
                        var err = SecKeyChain.Add(query);
                        _Key = query.ValueData.ToString();
                    }
                    else
                    {
                        _Key = uniqueId.ToString();
                    }
                }

                return _Key;
            }
        }
```

## <a id="binaries-end"></a>向最终用户分发生成的二进制文件之前将它模糊处理

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 移动客户端 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | [Crypto Obfuscation For .Net](http://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **步骤** | 应该模糊处理生成的二进制文件（apk 中的程序集），以阻止对程序集进行反向工程。可以使用 `CryptoObfuscator` 等工具实现此目的。 |

## <a id="cert"></a>将 clientCredentialType 设置为 Certificate 或 Windows

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | WCF | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | .NET Framework 3 |
| **属性**              | 不适用  |
| **参考**              | [Fortify](https://vulncat.fortify.com/en/vulncat/index.html) |
| **步骤** | 通过未加密的通道使用包含明文密码的 UsernameToken 会向探查 SOAP 消息的攻击者透露密码。 使用 UsernameToken 的服务提供程序可能会接受以明文形式发送的密码。 通过未加密的通道发送明文密码会向探查 SOAP 消息的攻击者透露凭据。 | 

### <a name="example"></a>示例
以下 WCF 服务提供程序配置使用 UsernameToken： 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
请将 clientCredentialType 设置为 Certificate 或 Windows。 

## <a id="security"></a>WCF - 安全模式未启用

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | WCF | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型、.NET Framework 3 |
| **属性**              | 安全模式 - 传输，安全模式 - 消息 |
| **参考**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx)、[巩固王国](https://vulncat.fortify.com/en/vulncat/index.html)、[WCF 安全基础知识 - CoDe 杂志](http://www.codemag.com/article/0611051) |
| **步骤** | 未定义任何传输或消息安全性。 在未定义传输或消息安全性的情况下传输消息的应用程序无法保证消息的完整性或机密性。 如果 WCF 安全绑定设置为 None，将同时禁用传输和消息安全性。 |

### <a name="example"></a>示例
以下配置将安全模式设置为 None。 
```
<system.serviceModel> 
  <bindings> 
    <wsHttpBinding> 
      <binding name=""MyBinding""> 
        <security mode=""None""/> 
      </binding> 
  </bindings> 
</system.serviceModel> 
```

### <a name="example"></a>示例
安全模式。在所有服务绑定中，有五种可能的安全模式： 
* 无。 关闭安全性。 
* 传输。 使用传输安全性进行相互身份验证和消息保护。 
* 消息。 使用消息安全性进行相互身份验证和消息保护。 
* 两者。 允许提供传输和消息级安全性的设置（只有 MSMQ 支持此模式）。 
* TransportWithMessageCredential。 通过消息传递凭据，消息保护和服务器身份验证由传输层提供。 
* TransportCredentialOnly。 通过传输层传递客户端凭据，不应用消息保护。 使用传输和消息安全性保护消息的完整性和保密性。 以下配置告知服务要对消息凭据使用传输安全性。
```
<system.serviceModel>
  <bindings>
    <wsHttpBinding>
    <binding name=""MyBinding""> 
    <security mode=""TransportWithMessageCredential""/> 
    <message clientCredentialType=""Windows""/> 
    </binding> 
  </bindings> 
</system.serviceModel> 
```
