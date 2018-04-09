---
title: Azure API 管理 API 导入中的限制和已知问题 | Microsoft Docs
description: 有关使用 Open API、WSDL 或 WADL 格式导入到 Azure API 管理的已知问题和限制的详细信息。
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apipm
ms.openlocfilehash: d4229a3ecbcf8aa044eb448290c243e9920bd5cb
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="api-import-restrictions-and-known-issues"></a>API 导入限制和已知问题
## <a name="about-this-list"></a>关于此列表
导入 API 时，可能会遇到一些限制或识别问题，需要对其进行纠正才能成功导入。 本文记录了这些限制或问题，并按照 API 的导入格式对其进行了组织。

## <a name="open-api"></a>Open API/Swagger
如果在导入 Open API 文档时遇到错误，请确保已通过使用 Azure 门户中的设计器（设计 - 前端 - Open API 规范编辑器）或使用第三方工具（例如 <a href="http://www.swagger.io">Swagger 编辑器</a>）对其进行了验证。

* **主机名** APIM 需要主机名属性。
* **基路径** APIM 需要基路径属性。
* **方案** APIM 需要方案数组。

> [!IMPORTANT]
> 有关与 OpenAPI 导入相关的重要信息和提示，请参阅[此文档](https://blogs.msdn.microsoft.com/apimanagement/2018/03/28/important-changes-to-openapi-import-and-export/)。

## <a name="wsdl"></a>WSDL
WSDL 文件用于生成 SOAP 直通 API，或用作 SOAP 到 REST API 的后端。

* **WSDL:Import** - 目前 APIM 不支持使用该属性的 API。 客户应将导入的元素合并到一个文档中。
* **包含多个部分的消息** - 目前 APIM 不支持这些类型的消息。
* **WCF wsHttpBinding** 使用 Windows Communication Foundation 创建的 SOAP 服务应使用 basicHttpBinding - 不支持 wsHttpBinding。
* **MTOM** 使用 MTOM 的服务<em>可能</em>工作。 目前暂未提供官方支持。
* APIM 不支持以递归方式定义的**递归**类型（例如，引用这些类型本身的数组）。

## <a name="wadl"></a>WADL
目前没有已知的 WADL 导入问题。
