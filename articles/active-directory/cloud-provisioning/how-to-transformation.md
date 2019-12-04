---
title: Azure AD Connect 云预配转换
description: 本文档介绍如何使用转换来更改默认属性映射。
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 0d37fdb4ad0d385914aecd4ca62be498c5c0e7c5
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794466"
---
# <a name="transformations"></a>转换

转换允许你使用云设置更改属性与 Azure AD 的同步方式的默认行为。  

若要执行此任务，您需要编辑该架构，然后通过 web 请求重新提交它。

有关云预配特性的详细信息，请参阅[了解 Azure AD 架构](concept-attributes.md)。


## <a name="retrieve-the-schema"></a>检索架构
若要检索该架构，请使用[查看架构](concept-attributes.md#viewing-the-schema)中所述的步骤。 


## <a name="custom-attribute-mapping"></a>自定义属性映射
若要添加自定义属性映射，请使用以下过程。

1. 将架构复制到文本或代码编辑器中，如[Visual Studio Code](https://code.visualstudio.com/)。  
2. 在架构中找到要更新的对象 ![](media/how-to-transformation/transform1.png)</br>
3. 在 user 对象下找到**ExtensionAttribute3**的代码。

    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([extensionAttribute3])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[extensionAttribute3]",
                                                "name": "extensionAttribute3",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
 4.  编辑代码，使 company 属性映射到 ExtensionAttribute3。
    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([company])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[company]",
                                                "name": "company",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
 5. 将架构复制回 Graph 资源管理器，将请求类型更改为 PUT 并**运行查询**。  
 ![](media/how-to-transformation/transform2.png)</br>
 6.  现在，在 Azure 门户中，导航到云预配配置并**重新启动预配**。
 ![](media/how-to-transformation/transform3.png)</br>
 7.  稍微经过一段时间后，通过在图形资源管理器中运行以下查询来验证正在填充的属性： `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`。
 8.  现在应会看到值。
 ![](media/how-to-transformation/transform4.png)</br>

## <a name="custom-attribute-mapping-with-function"></a>函数的自定义属性映射
对于更高级的映射，你可以使用允许操作数据的函数，并为属性创建值，以满足你的组织的需求。

若要执行此任务，只需执行上述步骤，然后编辑用于构造最终值的函数。

有关表达式的语法和示例的信息，请参阅[在 Azure Active Directory 中编写属性映射的表达式](reference-expressions.md)


## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)
