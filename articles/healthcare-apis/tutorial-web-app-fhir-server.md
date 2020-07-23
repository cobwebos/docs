---
title: Web 应用教程 - 设置 Azure API for FHIR
description: 本教程将演练部署简单 Web 应用程序的示例。 本第一篇教程介绍 Azure API for FHIR 的先决条件和部署
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: mihansen
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: fcad9356f87e76b14e0ef636389c11fd4290e12a
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "84870493"
---
# <a name="deploy-javascript-app-to-read-data-from-fhir-service"></a>部署用于从 FHIR 服务读取数据的 JavaScript 应用
在本教程中，你将部署一个用于从 FHIR 服务读取数据的小型 JavaScript 应用。 本教程中的步骤包括：
1. 部署 FHIR 服务器
1. 注册公共客户端应用程序
1. 测试对应用程序的访问
1. 创建用于读取此 FHIR 数据的 Web 应用程序

## <a name="prerequisites"></a>必备条件
在开始学习本套教程之前，需要准备好以下各项：
1. Azure 订阅
1. 一个 Azure Active Directory 租户
1. 已安装 [Postman](https://www.getpostman.com/)

> [!NOTE]
> 在本教程中，FHIR 服务、Azure AD 应用程序和 Azure AD 用户均位于同一 Azure AD 租户中。 如果不是这样，你仍可以遵循本教程的指导，但可能需要深入到一些参考文档来执行其他步骤。

## <a name="deploy-azure-api-for-fhir"></a>部署 Azure API for FHIR
本教程的第一个步骤是正确设置 Azure API for FHIR。

1. 部署 [Azure API for FHIR](fhir-paas-portal-quickstart.md)
1. 部署 Azure API for FHIR 后，请转到 Azure API for FHIR 并选择“CORS”，以配置 [CORS](configure-cross-origin-resource-sharing.md) 设置。 
    1. 将“源”设置为 * 
    1. 将“标头”设置为 * 
    1. 在“方法”下，选择“全选”  
    1. 将“最长期限”设置为 600  

## <a name="next-steps"></a>后续步骤
部署 Azure API for FHIR 后，接下来可以注册一个公共客户端应用程序。

>[!div class="nextstepaction"]
>[注册公共客户端应用程序](tutorial-web-app-public-app-reg.md)
