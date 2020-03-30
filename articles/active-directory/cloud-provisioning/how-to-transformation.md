---
title: Azure AD 连接云预配转换
description: 本文介绍如何使用转换来更改默认属性映射。
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: ec12927b40096b7ff04fae6b7cbc69a7bc11e8f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75549289"
---
# <a name="transformations"></a>转换

通过转换，可以使用云预配更改属性与 Azure 活动目录 （Azure AD） 同步方式的默认行为。

要执行此任务，您需要编辑架构，然后通过 Web 请求重新提交它。

有关云预配属性的详细信息，请参阅[了解 Azure AD 架构](concept-attributes.md)。


## <a name="retrieve-the-schema"></a>检索架构
若要检索架构，请按照[查看架构 中](concept-attributes.md#view-the-schema)的步骤操作。 

## <a name="custom-attribute-mapping"></a>自定义属性映射
要添加自定义属性映射，请按照以下步骤操作。

1. 将架构复制到文本或代码编辑器（如[Visual Studio 代码](https://code.visualstudio.com/)）。
1. 在架构中查找要更新的对象。

   ![架构中的对象](media/how-to-transformation/transform1.png)</br>
1. 找到用户对象`ExtensionAttribute3`下的代码。

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
1. 编辑代码，以便公司属性映射到`ExtensionAttribute3`。

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
 1. 将架构复制回图形资源管理器，将**请求类型**更改为**PUT，** 然后选择 **"运行查询**"。

    ![运行查询](media/how-to-transformation/transform2.png)

 1. 现在，在 Azure 门户中，转到云预配配置并选择 **"重新启动预配**"。

    ![重新启动预配](media/how-to-transformation/transform3.png)

 1. 稍等一段时间后，通过在图形资源管理器中运行以下查询来验证正在填充属性： `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`。
 1. 现在，您应该看到该值。

    ![显示该值](media/how-to-transformation/transform4.png)

## <a name="custom-attribute-mapping-with-function"></a>使用函数的自定义属性映射
对于更高级的映射，可以使用允许您操作数据并为属性创建值以满足组织需求的函数。

要执行此任务，请按照前面的步骤操作，然后编辑用于构造最终值的函数。

有关表达式的语法和示例的信息，请参阅[在 Azure 活动目录中编写属性映射的表达式](reference-expressions.md)。


## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)
