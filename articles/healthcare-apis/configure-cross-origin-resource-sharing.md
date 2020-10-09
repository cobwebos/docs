---
title: 在 Azure API for FHIR 中配置跨源资源共享
description: 本文介绍如何在 Azure API for FHIR 中配置跨源资源共享。
author: matjazl
ms.author: matjazl
ms.date: 3/11/2019
ms.topic: reference
ms.service: healthcare-apis
ms.subservice: fhir
ms.openlocfilehash: dc4c034b0821f1fe5ecb940591fca77d61edc3ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84870937"
---
# <a name="configure-cross-origin-resource-sharing-in-azure-api-for-fhir"></a>在 Azure API for FHIR 中配置跨源资源共享

适用于快速医疗保健互操作性资源 (FHIR) 的 Azure API 支持[跨源资源共享 (CORS)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing)。 CORS 可让你配置所需的设置，使一个域（源）中的应用程序可以访问另一个域中的资源，这称为跨域请求。

必须在另一个域中调用 RESTful API 的单页应用程序经常会使用 CORS。

若要在 Azure API for FHIR 中配置 CORS 设置，请指定以下设置：

- **源 (Access-Control-Allow-Origin)** 。 可向 Azure API for FHIR 发出跨源请求的域列表。 必须独行输入每个域（源）。 可以输入星号 (*) 以允许来自任何域的调用，但我们不建议这样做，因为这会带来安全风险。

- **标头(Access-Control-Allow-Headers)** 。 源请求将包含的标头列表。 若要允许所有标头，请输入星号 (*)。

- **方法(Access-Control-Allow-Methods)** 。 API 调用中允许的方法（PUT、GET、POST 等）。 选择“全选”会选择所有方法。 

- **最长期限(Access-Control-Max-Age)** 。 Access-Control-Allow-Headers 和 Access-Control-Allow-Methods 的预检请求结果的缓存期限值（以秒为单位）。

- **允许凭据(Access-Control-Allow-Credentials)** 。 CORS 请求一般不包含 Cookie，目的是防止[跨站点请求伪造 (CSRF)](https://en.wikipedia.org/wiki/Cross-site_request_forgery) 攻击。 如果选择此设置，则可以发出包括 Cookie 等凭据的请求。 如果已使用星号 (*) 设置了“源”，则无法配置此设置。

![跨源资源共享 (CORS) 设置](media/cors/cors.png)

>[!NOTE]
>不能为不同的域源指定不同的设置。 所有设置（“标头”、“方法”、“最长期限”和“允许凭据”）将应用到“源”设置中指定的所有源。    

## <a name="next-steps"></a>后续步骤

在本文，你已了解如何在 Azure API for FHIR 中配置跨源共享。 接下来请部署完全托管式的 Azure API for FHIR：
 
>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)