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
ms.openlocfilehash: 4d13d9dbea9da9db5bfe9a9af85fdbf9eab1ae84
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2017
ms.locfileid: "26127753"
---
# <a name="api-management-authentication-policies"></a>API 管理身份验证策略
本主题提供以下 API 管理策略的参考。 有关添加和配置策略的信息，请参阅 [API 管理中的策略](http://go.microsoft.com/fwlink/?LinkID=398186)。  

##  <a name="AuthenticationPolicies"></a>身份验证策略  
  
-   [使用基本方法进行身份验证](api-management-authentication-policies.md#Basic) - 使用基本身份验证方法对后端服务进行身份验证。  
  
-   [使用客户端证书进行身份验证](api-management-authentication-policies.md#ClientCertificate) - 使用客户端证书对后端服务进行身份验证。  
  
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
  
|Name|说明|必选|  
|----------|-----------------|--------------|  
|authentication-basic|根元素。|是|  
  
### <a name="attributes"></a>属性  
  
|Name|说明|必选|默认|  
|----------|-----------------|--------------|-------------|  
|username|指定基本凭据的用户名。|是|不适用|  
|password|指定基本凭据的密码。|是|不适用|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[段](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略段：** inbound  
  
-   **策略范围：** API  
  
##  <a name="ClientCertificate"></a>使用客户端证书进行身份验证  
 通过 `authentication-certificate` 策略使用客户端证书对后端服务进行身份验证。 需要首先将证书[安装到 API 管理](http://go.microsoft.com/fwlink/?LinkID=511599)，并由其指纹进行标识。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<authentication-certificate thumbprint="thumbprint" />  
```  
  
### <a name="example"></a>示例  
  
```xml  
<authentication-certificate thumbprint="....." />  
```  
  
### <a name="elements"></a>元素  
  
|Name|说明|必选|  
|----------|-----------------|--------------|  
|authentication-certificate|根元素。|是|  
  
### <a name="attributes"></a>属性  
  
|Name|说明|必选|默认|  
|----------|-----------------|--------------|-------------|  
|thumbprint|客户端证书的指纹。|是|不适用|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[段](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略段：** inbound  
  
-   **策略范围：** API  
  

## <a name="next-steps"></a>后续步骤
有关如何使用策略的详细信息，请参阅：

+ [API 管理中的策略](api-management-howto-policies.md)
+ [转换 API](transform-api.md)
+ [策略参考](api-management-policy-reference.md)，获取策略语句及其设置的完整列表
+ [策略示例](policy-samples.md)   
