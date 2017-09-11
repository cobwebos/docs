---
title: "通信安全 - Microsoft 威胁建模工具 - Azure | Microsoft 文档"
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
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: b85003a94df3b97f5c64eb3b7e62071f07674c5e
ms.contentlocale: zh-cn
ms.lasthandoff: 08/23/2017

---

# <a name="security-frame-communication-security--mitigations"></a>安全框架：通信安全 | 缓解措施 
| 产品/服务 | 文章 |
| --------------- | ------- |
| **Azure 事件中心** | <ul><li>[使用 SSL/TLS 保护与事件中心之间的通信](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[检查服务帐户特权，并检查自定义服务或 ASP.NET 页面是否遵循 CRM 的安全性](#priv-aspnet)</li></ul> |
| **Azure 数据工厂** | <ul><li>[将本地 SQL Server 连接到 Azure 数据工厂时使用数据管理网关](#sqlserver-factory)</li></ul> |
| **标识服务器** | <ul><li>[确保发往标识服务器的所有流量都通过 HTTPS 连接传输](#identity-https)</li></ul> |
| **Web 应用程序** | <ul><li>[验证用于对 SSL、TLS 和 DTLS 连接进行身份验证的 X.509 证书](#x509-ssltls)</li><li>[在 Azure 应用服务中为自定义域配置 SSL 证书](#ssl-appservice)</li><li>[强制要求发往 Azure 应用服务的所有流量都通过 HTTPS 连接传输](#appservice-https)</li><li>[启用 HTTP 严格传输安全性 (HSTS)](#http-hsts)</li></ul> |
| **数据库** | <ul><li>[确保加密 SQL Server 连接并验证证书](#sqlserver-validation)</li><li>[强制以加密形式来与 SQL Server 通信](#encrypted-sqlserver)</li></ul> |
| **Azure 存储** | <ul><li>[确保与 Azure 存储之间的通信通过 HTTPS 进行](#comm-storage)</li><li>[如果无法启用 HTTPS，请在下载 Blob 后验证 MD5 哈希](#md5-https)</li><li>[使用 SMB 3.0 兼容的客户端来确保传输到 Azure 文件共享的数据经过加密](#smb-shares)</li></ul> |
| **移动客户端** | <ul><li>[实施证书绑定](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[启用 HTTPS - 安全传输通道](#https-transport)</li><li>[WCF：将消息安全保护级别设置为 EncryptAndSign](#message-protection)</li><li>[WCF：使用最低特权帐户运行 WCF 服务](#least-account-wcf)</li></ul> |
| **Web API** | <ul><li>[强制要求发往 Web API 的所有流量都通过 HTTPS 连接传输](#webapi-https)</li></ul> |
| **Azure Redis 缓存** | <ul><li>[确保与 Azure Redis 缓存之间的通信通过 SSL 进行](#redis-ssl)</li></ul> |
| **IoT 现场网关** | <ul><li>[保护设备与现场网关之间的通信](#device-field)</li></ul> |
| **IoT 云网关** | <ul><li>[使用 SSL/TLS 保护设备与云网关之间的通信](#device-cloud)</li></ul> |

## <a id="comm-ssltls"></a>使用 SSL/TLS 保护与事件中心之间的通信

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Azure 事件中心 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | [事件中心身份验证和安全模型概述](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **步骤** | 使用 SSL/TLS 来保护与事件中心的 AMQP 或 HTTP 连接 |

## <a id="priv-aspnet"></a>检查服务帐户特权，并检查自定义服务或 ASP.NET 页面是否遵循 CRM 的安全性

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Dynamics CRM | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | 检查服务帐户特权，并检查自定义服务或 ASP.NET 页面是否遵循 CRM 的安全性 |

## <a id="sqlserver-factory"></a>将本地 SQL Server 连接到 Azure 数据工厂时使用数据管理网关

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Azure 数据工厂 | 
| **SDL 阶段**               | 部署 |  
| **适用的技术** | 泛型 |
| **属性**              | 链接服务类型 - Azure 和本地 |
| **参考**              |[在本地与 Azure 数据工厂之间移动数据](https://azure.microsoft.com/documentation/articles/data-factory-move-data-between-onprem-and-cloud/#create-gateway)、[数据管理网关](https://azure.microsoft.com/documentation/articles/data-factory-data-management-gateway/) |
| **步骤** | <p>需要使用数据管理网关 (DMG) 工具连接到受企业网络或防火墙保护的数据源。</p><ol><li>锁定计算机可以隔离 DMG 工具，防止不正常的程序损坏源计算机或者窥视其数据。 （例如， 必须安装最新的更新、启用所需的最少量端口、预配受控帐户、审核启用、启用磁盘加密，等等。）</li><li>必须经常或者每当 DMG 服务帐户密码续订时轮替数据网关密钥</li><li>通过链接服务传输的数据必须加密</li></ol> |

## <a id="identity-https"></a>确保发往标识服务器的所有流量都通过 HTTPS 连接传输

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 标识服务器 | 
| **SDL 阶段**               | 部署 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | [IdentityServer3 - 密钥、签名和加密](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html)、[IdentityServer3 - 部署](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **步骤** | 默认情况下，IdentityServer 要求所有传入连接都通过 HTTPS 建立。 只能通过受保护的传输来与 IdentityServer 通信，是一项绝对需要遵守的要求。 在某些部署方案（例如 SSL 卸载）中，可以放宽此项要求。 有关详细信息，请参阅“参考”部分中的标识服务器部署页。 |

## <a id="x509-ssltls"></a>验证用于对 SSL、TLS 和 DTLS 连接进行身份验证的 X.509 证书

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | <p>使用 SSL、TLS 或 DTLS 的应用程序必须全面验证它们所要连接到的实体的 X.509 证书。 这包括验证证书的以下信息：</p><ul><li>域名</li><li>生效日期（开始日期和过期日期）</li><li>吊销状态</li><li>用途（例如，对服务器进行服务器身份验证，对客户端进行客户端身份验证）</li><li>信任链。 证书必须链接到平台信任的或者由管理员显式配置的根证书颁发机构 (CA)</li><li>证书公钥的密钥长度必须 >2048 位</li><li>哈希算法必须是 SHA256 和更高级别 |

## <a id="ssl-appservice"></a>在 Azure 应用服务中为自定义域配置 SSL 证书

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | EnvironmentType - Azure |
| **参考**              | [为 Azure 应用服务中的应用启用 HTTPS](https://azure.microsoft.com/documentation/articles/web-sites-configure-ssl-certificate/) |
| **步骤** | 默认情况下，Azure 已使用 *.azurewebsites.net 域的通配符证书为每个应用启用了 HTTPS。 但是，就像所有通配符域一样，这不如将自定义域与自己的证书配合使用那么安全。[参考](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/) 建议针对用于访问所部署应用的自定义域启用 SSL|

## <a id="appservice-https"></a>强制要求发往 Azure 应用服务的所有流量都通过 HTTPS 连接传输

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | EnvironmentType - Azure |
| **参考**              | [在 Azure 应用服务中强制 HTTPS]https://azure.microsoft.com/documentation/articles/web-sites-configure-ssl-certificate/#4-enforce-https-on-your-app) |
| **步骤** | <p>尽管 Azure 已使用 *.azurewebsites.net 域的通配符证书为 Azure 应用服务启用了 HTTPS，但它并不强制 HTTPS。 访问者仍可使用 HTTP 访问应用，这可能会损害应用的安全性，因此必须显式强制 HTTPS。 ASP.NET MVC 应用程序应使用 [RequireHttps 筛选器](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx)，强制要求通过 HTTPS 重新发送不安全的 HTTP 请求。</p><p>或者，可以使用 Azure 应用服务随附的 URL 重写模块来强制 HTTPS。 开发人员可以使用 URL 重写模块来定义将请求传递给应用程序之前应用到传入请求的规则。 URL 重写规则在 web.config 文件中定义，该文件存储在应用程序根目录中。</p>|

### <a name="example"></a>示例
以下示例包含可强制所有传入流量使用 HTTPS 的基本 URL 重写规则
```XML
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```
此规则的工作方式是当用户使用 HTTP 请求某个页面时，返回 HTTP 状态码 301（永久重定向）。 301 将请求重定向到访问者请求的同一个 URL，但使用 HTTPS 来替换请求的 HTTP 部分。 例如，HTTP://contoso.com 将重定向到 HTTPS://contoso.com。 

## <a id="http-hsts"></a>启用 HTTP 严格传输安全性 (HSTS)

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | [OWASP HTTP 严格传输安全性速查表](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) |
| **步骤** | <p>HTTP 严格传输安全性 (HSTS) 是 Web 应用程序使用特殊响应标头指定的一个选用的安全增强功能。 支持的浏览器收到此标头后，将阻止通过 HTTP 将任何通信发送到指定的域，并改为通过 HTTPS 发送所有通信。 它还可以防止浏览器中出现 HTTPS 点击提示。</p><p>若要实现 HSTS，必须在代码或配置中为网站全局配置以下响应标头。Strict-Transport-Security: max-age=300; includeSubDomains。HSTS 可解决以下威胁：</p><ul><li>用户将 http://example.com 加入书签或者手动键入该 URL 时，可能会受到中间人攻击：HSTS 会自动将 HTTP 请求重定向到目标域的 HTTPS</li><li>纯粹只进行 HTTPS 通信的 Web 应用程序无意中包含 HTTP 链接或通过 HTTP 提供内容：HSTS 会自动将 HTTP 请求重定向到目标域的 HTTPS</li><li>中间人攻击者尝试使用无效的证书来截获受害用户发送的流量，并希望该用户接受错误的证书：HSTS 不允许用户重写无效的证书消息</li></ul>|

## <a id="sqlserver-validation"></a>确保加密 SQL Server 连接并验证证书

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 数据库 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | SQL Azure  |
| **属性**              | SQL 版本 - V12 |
| **参考**              | [有关为 SQL 数据库编写安全连接字符串的最佳做法](http://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **步骤** | <p>SQL 数据库与客户端应用程序之间的所有通信始终使用安全套接字层 (SSL) 加密。 SQL 数据库不支持未加密的连接。 若要使用应用程序代码或工具验证证书，需显式请求一个加密的连接并且不信任服务器证书。 即使应用程序代码或工具未请求加密的连接，它们仍会收到加密的连接</p><p>但是，它们可能不会验证服务器证书，因此将容易受到“中间人”攻击。 若要使用 ADO.NET 应用程序代码验证证书，请在数据库连接字符串中设置 `Encrypt=True` 和 `TrustServerCertificate=False`。 若要通过 SQL Server Management Studio 验证证书，请打开“连接到服务器”对话框。 在“连接属性”选项卡中单击“加密连接”</p>|

## <a id="encrypted-sqlserver"></a>强制以加密形式来与 SQL Server 通信

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 数据库 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | OnPrem |
| **属性**              | SQL 版本 - MsSQL2016，SQL 版本 - MsSQL2012，SQL 版本 - MsSQL2014 |
| **参考**              | [启用与数据库引擎的加密连接](https://msdn.microsoft.com/library/ms191192)  |
| **步骤** | 启用 SSL 加密可以提高在 SQL Server 实例与应用程序之间通过网络传输的数据的安全性。 |

## <a id="comm-storage"></a>确保与 Azure 存储之间的通信通过 HTTPS 进行

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Azure 存储 | 
| **SDL 阶段**               | 部署 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | [Azure 存储传输级加密 – 使用 HTTPS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_encryption-in-transit) |
| **步骤** | 为了确保传输中 Azure 存储数据的安全性，请在调用 REST API 或访问存储中的对象时，始终使用 HTTPS 协议。 此外，可以使用共享访问签名，它除了可以委派对 Azure 存储对象的访问权限，还能指定在使用共享访问签名时只能使用 HTTPS 协议，确保任何使用 SAS 令牌发出链接的人都使用正确的协议。|

## <a id="md5-https"></a>如果无法启用 HTTPS，请在下载 Blob 后验证 MD5 哈希

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Azure 存储 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | StorageType - Blob |
| **参考**              | [Windows Azure Blob MD5 概述](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **步骤** | <p>Windows Azure Blob 服务提供相应的机制来确保应用程序和传输层的数据完整性。 如果出于任何原因需要使用 HTTP 而不是 HTTPS，并且使用的是块 Blob，则可以使用 MD5 检查，帮助验证正在传输的 Blob 的完整性。</p><p>这将有助于防止网络/传输层错误，但不一定可帮助防止中间攻击。 如果可以使用提供传输级安全的 HTTPS，则使用 MD5 检查就很多余且不必要。</p>|

## <a id="smb-shares"></a>使用与 SMB 3.0 兼容的客户端来确保传输到 Azure 文件共享的数据经过加密

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 移动客户端 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | StorageType - 文件 |
| **参考**              | [Azure 文件存储](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931)、[Windows 客户端的 Azure 文件存储 SMB 支持](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#_mount-the-file-share) |
| **步骤** | 使用 REST API 时，Azure 文件存储支持 HTTPS，但经常用作附加到 VM 的 SMB 文件共享。 SMB 2.1 不支持加密，因此只允许在 Azure 中的相同区域内连接。 但是，SMB 3.0 支持加密，并且可以配合 Windows Server 2012 R2、Windows 8、Windows 8.1 和 Windows 10 使用，允许跨区域访问，甚至桌面上的访问。 |

## <a id="cert-pinning"></a>实施证书固定

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Azure 存储 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 通用、Windows Phone |
| **属性**              | 不适用  |
| **参考**              | [证书和公钥绑定](https://www.owasp.org/index.php/Certificate_and_Public_Key_Pinning#.Net) |
| **步骤** | <p>证书绑定可以防范中间人 (MITM) 攻击。 绑定是将主机与其预期 X509 证书或公钥相关联的过程。 某个主机知悉或者识别到某个证书或公钥后，该证书或公钥将关联或“绑定”到该主机。 </p><p>因此，当攻击者尝试展开 SSL MITM 攻击时，在 SSL 握手期间，攻击者服务器中的密钥将与绑定证书的密钥不同，因此会丢弃该请求，阻止 MITM。可以通过实现 ServicePointManager 的 `ServerCertificateValidationCallback` 委派来完成证书固定。</p>|

### <a name="example"></a>示例
```C#
using System;
using System.Net;
using System.Net.Security;
using System.Security.Cryptography;

namespace CertificatePinningExample
{
    class CertificatePinningExample
    {
        /* Note: In this example, we're hardcoding a the certificate's public key and algorithm for 
           demonstration purposes. In a real-world application, this should be stored in a secure
           configuration area that can be updated as needed. */

        private static readonly string PINNED_ALGORITHM = "RSA";

        private static readonly string PINNED_PUBLIC_KEY = "3082010A0282010100B0E75B7CBE56D31658EF79B3A1" +
            "294D506A88DFCDD603F6EF15E7F5BCBDF32291EC50B2B82BA158E905FE6A83EE044A48258B07FAC3D6356AF09B2" +
            "3EDAB15D00507B70DB08DB9A20C7D1201417B3071A346D663A241061C151B6EC5B5B4ECCCDCDBEA24F051962809" +
            "FEC499BF2D093C06E3BDA7D0BB83CDC1C2C6660B8ECB2EA30A685ADE2DC83C88314010FFC7F4F0F895EDDBE5C02" +
            "ABF78E50B708E0A0EB984A9AA536BCE61A0C31DB95425C6FEE5A564B158EE7C4F0693C439AE010EF83CA8155750" +
            "09B17537C29F86071E5DD8CA50EBD8A409494F479B07574D83EDCE6F68A8F7D40447471D05BC3F5EAD7862FA748" +
            "EA3C92A60A128344B1CEF7A0B0D94E50203010001";


        public static void Main(string[] args)
        {
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("https://azure.microsoft.com");
            request.ServerCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) =>
            {
                if (certificate == null || sslPolicyErrors != SslPolicyErrors.None)
                {
                    // Error getting certificate or the certificate failed basic validation
                    return false;
                }

                var targetKeyAlgorithm = new Oid(certificate.GetKeyAlgorithm()).FriendlyName;
                var targetPublicKey = certificate.GetPublicKeyString();
                
                if (targetKeyAlgorithm == PINNED_ALGORITHM &&
                    targetPublicKey == PINNED_PUBLIC_KEY)
                {
                    // Success, the certificate matches the pinned value.
                    return true;
                }
                // Reject, either the key or the algorithm does not match the expected value.
                return false;
            };

            try
            {
                var response = (HttpWebResponse)request.GetResponse();
                Console.WriteLine($"Success, HTTP status code: {response.StatusCode}");
            }
            catch(Exception ex)
            {
                Console.WriteLine($"Failure, {ex.Message}");
            }
            Console.WriteLine("Press any key to end.");
            Console.ReadKey();
        }
    }
}
```

## <a id="https-transport"></a>启用 HTTPS - 安全传输通道

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | WCF | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | NET Framework 3 |
| **属性**              | 不适用  |
| **参考**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx)、[巩固王国](https://vulncat.fortify.com/en/vulncat/index.html) |
| **步骤** | 应用程序配置应确保始终使用 HTTPS 来访问敏感信息。<ul><li>**说明：**如果应用程序需要处理敏感信息但未使用消息级加密，则只能允许它通过加密的传输通道来通信。</li><li>**建议：**确保禁用 HTTP 传输，改为启用 HTTPS 传输。 例如，将 `<httpTransport/>` 替换为 `<httpsTransport/>` 标记。 不要依赖使用网络配置（防火墙）来保证只能通过安全通道访问应用程序。 从哲学的观点来讲，应用程序不应依赖于网络来保证其安全性。</li></ul><p>从实践的观点来讲，负责保护网络的人不会一直跟进应用程序的不断变化的安全要求。</p>|

## <a id="message-protection"></a>WCF：将消息安全保护级别设置为 EncryptAndSign

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | WCF | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | .NET Framework 3 |
| **属性**              | 不适用  |
| **参考**              | [MSDN](https://msdn.microsoft.com/library/ff650862.aspx) |
| **步骤** | <ul><li>**说明：**当保护级别设置为“none”时，将禁用消息保护。 保密性和完整性是使用适当的设置级别实现的。</li><li>**建议：**<ul><li>当 `Mode=None` 时 - 禁用消息保护</li><li>当 `Mode=Sign` 时 - 将消息签名但不加密；当数据完整性非常重要时应使用该设置</li><li>当 `Mode=EncryptAndSign` 时 - 将消息签名并加密</li></ul></li></ul><p>请考虑禁用加密，仅当只是需要验证信息的完整性而不关心机密性时，才为消息签名。 对于需要验证原始发送者但不传输任何敏感数据的操作或服务约定，这种做法可能很有用。 降低保护级别时，请小心不要在消息中包含任何个人身份信息 (PII)。</p>|

### <a name="example"></a>示例
以下示例演示了如何将服务和操作配置为只将消息签名。 `ProtectionLevel.Sign` 的服务约定示例：下面是在服务约定级别使用 ProtectionLevel.Sign 的示例： 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>示例
`ProtectionLevel.Sign` 的操作约定示例（用于精细控制）：下面是在操作约定级别使用 `ProtectionLevel.Sign` 的示例：

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a id="least-account-wcf"></a>WCF：使用最低特权帐户运行 WCF 服务

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | WCF | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | .NET Framework 3 |
| **属性**              | 不适用  |
| **参考**              | [MSDN](https://msdn.microsoft.com/library/ff648826.aspx ) |
| **步骤** | <ul><li>**说明：**不要使用管理员或高特权帐户运行 WCF 服务。 否则，如果服务遭到入侵，将导致严重影响。</li><li>**建议：**最低特权帐户托管 WCF 服务，因为这样可以在遭到攻击时减小应用程序的受攻击面，降低潜在损失。 如果服务帐户需要 MSMQ、事件日志、性能计数器和文件系统等基础结构资源的其他访问权限，应该授予对这些资源的相应权限，使 WCF 服务能够成功运行。</li></ul><p>如果服务需要代表原始调用方访问特定的资源，请使用模拟和委派来传送调用方的标识，以便在下游进行授权检查。 在开发方案中，请使用本地网络服务帐户，这是一个特权降低的特殊内置帐户。 在生产方案中，请创建最低特权的自定义域服务帐户。</p>|

## <a id="webapi-https"></a>强制要求发往 Web API 的所有流量都通过 HTTPS 连接传输

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web API | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | MVC5、MVC6 |
| **属性**              | 不适用  |
| **参考**              | [在 Web API 控制器中强制 SSL](http://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **步骤** | 如果应用程序同时使用 HTTPS 和 HTTP 绑定，则客户端仍可使用 HTTP 访问站点。 为了防止这种问题，请使用操作筛选器来确保始终通过 HTTPS 向受保护 API 传输请求。|

### <a name="example"></a>示例 
以下代码演示了一个检查 SSL 的 Web API 身份验证筛选器： 
```C#
public class RequireHttpsAttribute : AuthorizationFilterAttribute
{
    public override void OnAuthorization(HttpActionContext actionContext)
    {
        if (actionContext.Request.RequestUri.Scheme != Uri.UriSchemeHttps)
        {
            actionContext.Response = new HttpResponseMessage(System.Net.HttpStatusCode.Forbidden)
            {
                ReasonPhrase = "HTTPS Required"
            };
        }
        else
        {
            base.OnAuthorization(actionContext);
        }
    }
}
```
将此筛选器添加到要求使用 SSL 的任何 Web API 操作： 
```C#
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a id="redis-ssl"></a>确保与 Azure Redis 缓存之间的通信通过 SSL 进行

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Azure Redis 缓存 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | [Azure Redis SSL 支持](https://azure.microsoft.com/documentation/articles/cache-faq/#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis) |
| **步骤** | Redis 服务器不能现成地支持 SSL，但 Azure Redis 缓存可提供此支持。 如果要连接到 Azure Redis 缓存并且客户端支持 SSL（如 StackExchange.Redis），则你应使用 SSL。 默认将为新的 Azure Redis 缓存实例禁用非 SSL 端口。 请确保安全的默认设置不会更改，除非 Redis 客户端依赖 SSL 支持。 |

请注意，Redis 旨在由受信任环境中的受信任客户端访问。 这意味着，我们通常不建议将 Redis 实例直接在 Internet 中公开，一般情况下，在不受信任的客户端可以直接访问 Redis TCP 端口或 UNIX 套接字的环境中，也不建议公开 Redis 实例。 

## <a id="device-field"></a>保护设备与现场网关之间的通信

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | IoT 现场网关 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | 对于基于 IP 的设备，通常可将通信协议封装在 SSL/TLS 通道中，以保护传输中的数据。 对于其他不支持 SSL/TLS 的协议，请调查是否有安全的协议版本可在传输或消息层提供安全性。 |

## <a id="device-cloud"></a>使用 SSL/TLS 保护设备与云网关之间的通信

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | IoT 云网关 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | [选择通信协议](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#messaging) |
| **步骤** | 使用 SSL/TLS 保护 HTTP/AMQP 或 MQTT 协议。 |

