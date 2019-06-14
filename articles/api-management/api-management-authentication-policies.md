---
title: Azure API 管理身份验证策略 | Microsoft Docs
description: 了解可在 Azure API 管理中使用的身份验证策略。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: c0f8da779ca656cf357c418b8766a53307643695
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64708796"
---
# <a name="api-management-authentication-policies"></a>API 管理身份验证策略
本主题提供以下 API 管理策略的参考。 有关添加和配置策略的信息，请参阅 [API 管理中的策略](https://go.microsoft.com/fwlink/?LinkID=398186)。  

##  <a name="AuthenticationPolicies"></a> 身份验证策略  
  
-   [使用基本方法进行身份验证](api-management-authentication-policies.md#Basic) - 使用基本身份验证方法向后端服务进行身份验证。  
  
-   [使用客户端证书进行身份验证](api-management-authentication-policies.md#ClientCertificate) - 使用客户端证书对后端服务进行身份验证。  

-   [使用托管的标识进行身份验证](api-management-authentication-policies.md#ManagedIdentity)-使用进行身份验证[托管标识](../active-directory/managed-identities-azure-resources/overview.md)API 管理服务。  
  
##  <a name="Basic"></a>使用基本方法进行身份验证  
 通过 `authentication-basic` 策略使用基本身份验证方法对后端服务进行身份验证。 此策略有效地将 HTTP 授权标头设置为与策略中提供的凭据对应的值。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<authentication-basic username="username" password="password" />  
```  
  
### <a name="example"></a>示例  
  
```xml  
<authentication-basic username="testuser" password="testpassword" />  
```  
  
### <a name="elements"></a>元素  
  
|Name|描述|必选|  
|----------|-----------------|--------------|  
|authentication-basic|根元素。|是|  
  
### <a name="attributes"></a>属性  
  
|Name|描述|需要|默认|  
|----------|-----------------|--------------|-------------|  
|username|指定基本凭据的用户名。|是|不适用|  
|password|指定基本凭据的密码。|是|不适用|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[段](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略段：** 入站  
  
-   **策略范围：** API  
  
##  <a name="ClientCertificate"></a>使用客户端证书进行身份验证  
 通过 `authentication-certificate` 策略使用客户端证书对后端服务进行身份验证。 需要首先将证书[安装到 API 管理](https://go.microsoft.com/fwlink/?LinkID=511599)，并由其指纹进行标识。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>  
```  
  
### <a name="examples"></a>示例  
  
在此示例中，客户端证书是由指纹标识的。
```xml  
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />  
``` 
在此示例中，客户端证书是由资源名称标识的。
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>元素  
  
|Name|描述|需要|  
|----------|-----------------|--------------|  
|authentication-certificate|根元素。|是|  
  
### <a name="attributes"></a>属性  
  
|Name|描述|需要|默认|  
|----------|-----------------|--------------|-------------|  
|thumbprint|客户端证书的指纹。|必须提供 `thumbprint` 或 `certificate-id`。|不适用|  
|certificate-id|证书资源名称。|必须提供 `thumbprint` 或 `certificate-id`。|不适用|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[段](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略段：** 入站  
  
-   **策略范围：** API  

##  <a name="ManagedIdentity"></a> 使用托管标识进行身份验证  
 使用 `authentication-managed-identity` 策略通过 API 管理服务的托管标识向后端服务进行身份验证。 此策略有效地使用托管标识来从 Azure Active Directory 获取用于访问指定资源的访问令牌。 
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>示例  
  
```xml  
<authentication-managed-identity resource="https://graph.windows.net" output-token-variable-name="test-access-token" ignore-error="true" /> 
```  
  
### <a name="elements"></a>元素  
  
|Name|描述|必选|  
|----------|-----------------|--------------|  
|authentication-managed-identity |根元素。|是|  
  
### <a name="attributes"></a>属性  
  
|Name|描述|需要|默认|  
|----------|-----------------|--------------|-------------|  
|resource|字符串。 Azure Active Directory 中的目标 Web API（受保护的资源）的应用 ID URI。|是|不适用|  
|output-token-variable-name|字符串。 上下文变量的名称，它将令牌值接收为对象类型 `string`。|否|不适用|  
|ignore-error|布尔值。 如果设置为 `true`，即使未获得访问令牌，策略管道也将继续执行。|否|false|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[段](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略段：** 入站  
  
-   **策略范围：** 全局、产品、API、操作  

## <a name="next-steps"></a>后续步骤
有关如何使用策略的详细信息，请参阅：

+ [API 管理中的策略](api-management-howto-policies.md)
+ [转换 API](transform-api.md)
+ [策略参考](api-management-policy-reference.md)，获取策略语句及其设置的完整列表
+ [策略示例](policy-samples.md)   
