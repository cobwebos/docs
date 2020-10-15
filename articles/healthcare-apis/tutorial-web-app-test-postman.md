---
title: Web 应用教程 - 测试连接到 Azure API for FHIR
description: 这些教程将演练部署简单 Web 应用程序的示例。 本教程部分将演练如何使用 Postman 连接到 FHIR 服务器
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 1c64468a2e420734ca51a5b9308bb52e13712c51
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87852916"
---
# <a name="testing-the-fhir-api"></a>测试 FHIR API
在前两个步骤中，你已部署 Azure API for FHIR 并注册了客户端应用程序。 现在，可以测试客户端应用程序中是否设置了 Azure API for FHIR。 

## <a name="retrieve-capability-statement"></a>检索功能语句
首先，我们将获取 Azure API for FHIR 的功能语句。 
1. 打开 Postman
1. 通过执行 GET https://\<FHIR-SERVER-NAME>.azurehealthcareapis.com/metadata 检索功能声明。 在下图中，FHIR 服务器名称为 **fhirserver**。

![功能语句](media/tutorial-web-app/postman-capability-statement.png)

接下来，我们尝试检索患者。 若要检索患者，请输入 GET https://\<FHIR-SERVER-NAME>.azurehealthcareapis.com/Patient。 你会收到“401 未授权”错误。 出现此错误的原因是你尚未证明你有权访问患者数据。

## <a name="get-patient-from-fhir-server"></a>从 FHIR 服务器获取患者
![检索患者失败](media/tutorial-web-app/postman-patient-authorization-failed.png)

若要获取访问权限，需要一个访问令牌。
1. 在 Postman 中选择“授权”，将“类型”设置为“OAuth2.0”  
1. 选择“获取新访问令牌” 
1. 填写字段，然后选择“请求令牌”。  在下面可以看到本教程中所述每个字段的值。

|字段                |值                                                               |
|---------------------|--------------------------------------------------------------------|
|令牌名称           |令牌的名称                                               |
|授权类型           |授权代码                                                  |
|回调 URL         |https://www.getpostman.com/oauth2/callback                          |
|身份验证 URL             |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID> /oauth2/?resource=https://\<FHIR-SERVER-NAME>.azurehealthcareapis.com|
|访问令牌 URL     |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID> /oauth2/token|
|客户端 ID            |在前面的步骤中复制的客户端 ID             |
|客户端机密        |\<BLANK>                                                            |
|范围                |\<BLANK>                                                            |
|状态                |1234                                                                |
|客户端身份验证|在正文中发送客户端凭据                                     |

4. 使用凭据登录，然后选择“接受” 
1. 向下滚动浏览结果，然后选择“使用令牌” 
1. 再次选择顶部的“发送”，这一次应会收到“检索患者成功”结果  ![](media/tutorial-web-app/postman-patient-authorization-success.png)

## <a name="post-patient-into-fhir-server"></a>将患者发布到 FHIR 服务器中
现已获取访问权限，接下来可以创建新患者。 下面是可以添加到 FHIR 服务器中的简单患者示例。 将以下代码输入到 Postman 的“正文”部分。 

``` json
    {
        "resourceType": "Patient",
        "active": true,
        "name": [
            {
                "use": "official",
                "family": "Kirk",
                "given": [
                    "James",
                    "Tiberious"
                ]
            },
            {
                "use": "usual",
                "given": [
                    "Jim"
                ]
            }
        ],
        "gender": "male",
        "birthDate": "1960-12-25"
    }
```
此 POST 命令将在 FHIR 服务器中创建名为 James Tiberious Kirk 的新患者。
![发布患者](media/tutorial-web-app/postman-post-patient.png)

如果再次执行上述 GET 步骤来检索患者，将会看到输出中列出了 James Tiberious Kirk。

## <a name="troubleshooting-access-issues"></a>排查访问问题
如果在执行上述任何步骤期间遇到问题，请查看我们汇总的有关 Azure Active Directory 和 Azure API for FHIR 的文档。 

* [Azure AD 和 Azure API for FHIR](azure-ad-hcapi.md) - 此文档概述了 Azure Active Directory 的一些基本原理，以及它如何与 Azure API for FHIR 交互。
* [访问令牌验证](azure-ad-hcapi-token-validation.md) - 此操作指南提供了有关访问令牌验证的详细信息，以及解决访问问题可以采取的步骤。

## <a name="next-steps"></a>后续步骤
既然你可以成功连接到客户端应用程序，接下来可以编写 Web 应用程序。

>[!div class="nextstepaction"]
>[编写 Web 应用程序](tutorial-web-app-write-web-app.md)



