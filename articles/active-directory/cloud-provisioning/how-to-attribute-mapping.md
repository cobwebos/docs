---
title: Azure AD Connect 云预配特性编辑器
description: 本文介绍如何使用 "属性编辑器"。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/22/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58b7aa0bf8c82990a00b4e41041145a67ee2f02b
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91637074"
---
# <a name="azure-ad-connect-cloud-provisioning-attibute-mapping"></a>Azure AD Connect 云预配属性映射

Azure AD Connect 云预配引入了一项新功能，可让你轻松地在本地用户/组对象与 Azure AD 中的对象之间映射属性。  此功能已添加到云预配配置。

可以根据业务需求自定义默认的属性映射。 因此，可以更改或删除现有属性映射，或创建新的属性映射。  有关同步的属性列表，请参阅 [已同步的属性](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)。

## <a name="understanding-attribute-mapping-types"></a>了解属性映射类型
通过属性映射，可以控制如何在 Azure AD 中填充属性。
支持四种不同的映射类型：

- **Direct** –目标属性使用 AD 中的链接对象的属性值进行填充。
- 常量 - 目标属性使用你指定的特定字符串填充。
- **表达式** - 目标属性是基于脚本式表达式的结果填充的。
  有关详细信息，请参阅 [编写属性映射的表达式](reference-expressions.md)。
- **无** - 目标属性保持不变。 不过，如果目标属性曾为空，则它由你指定的默认值填充。

除了这四个基本类型之外，自定义属性映射还支持可选的默认值赋值概念。 默认值赋值确保当 Azure AD 或目标对象中没有值时使用某个值填充目标属性。 最常见的配置是将此项留空。

## <a name="understanding-attribute-mapping-properties"></a>了解属性映射的属性

前面的部分介绍了属性映射类型属性。
除此属性外，属性映射还支持以下属性：

- **源属性** -来自源系统的用户属性 (示例： Active Directory) 。
- **目标属性** –目标系统中的用户属性 (示例： Azure Active Directory) 。
- 为 NULL 时填入默认值（可选） - 如果源属性为 NULL，将传递给目标系统的值。 只有在创建用户时才会预配此值。 更新现有用户时，不会预配“为 NULL 时填入默认值”。  
- **应用此映射**
  - 始终 - 对用户创建和更新操作均应用此映射。
  - 仅创建期间 - 仅对用户创建操作应用此映射。

> [!NOTE]
> 本文档介绍如何使用 Azure 门户映射属性。  有关使用关系图的信息，请参阅 [转换](how-to-transformation.md)

## <a name="using-attribute-mapping"></a>使用属性映射
若要使用新功能，请执行以下步骤。

 1.  在 Azure 门户中，选择“Azure Active Directory”。
 2.  选择“Azure AD Connect”****。
 3.  选择 " **管理设置**"。

   ![管理预配](media/how-to-configure/manage1.png)
 
 4. 在 " **配置**" 下，选择您的配置。
 5. 选择 **单击 "编辑映射"**。  此时将打开 "属性映射" 屏幕。

 ![添加属性](media/how-to-attribute-mapping/mapping6.png)
 6.  单击 " **添加属性**"。

 ![映射类型](media/how-to-attribute-mapping/mapping1.png)
 
 7. 选择 **映射类型**。  在此示例中，我们使用 Expression。
 8.  在框中输入表达式。  在此示例中，我们使用： `Replace([mail], "@contoso.com", , ,"", ,).`
 9.  输入目标属性。  在此示例中，我们使用 ExtensionAttribute15。
 10. 选择要应用的时间，然后单击 "**应用**"
   
   ![编辑映射](media/how-to-attribute-mapping/mapping2a.png)
 11. 返回 "属性映射" 屏幕时，应会看到新的属性映射。  
 12. 单击 " **保存架构**"。

 ![保存架构](media/how-to-attribute-mapping/mapping3.png)

## <a name="test-your-attribute-mapping"></a>测试您的属性映射
若要测试您的属性映射，可以使用 [按需设置](how-to-on-demand-provision.md)。  从 

1.  在 Azure 门户中，选择“Azure Active Directory”。
2.  选择“Azure AD Connect”****。
3.  选择 " **管理设置**"。
4. 在 " **配置**" 下，选择您的配置。
5. 在 " **验证** " 下单击 " **设置用户** " 按钮。 
6. 在 "按需设置" 屏幕上。  输入用户或组的 **可分辨名称** ，然后单击 " **设置** " 按钮。  
7. 完成后，应会看到 "成功" 屏幕和4个绿色复选框，指示已成功设置。  
  ![预配成功](media/how-to-attribute-mapping/mapping4.png)
1. 在 " **执行操作** " 下，单击 " **查看详细信息**"。  在右侧，应会看到新属性 syncrhonized 和应用的表达式。

  ![执行操作](media/how-to-attribute-mapping/mapping5.png)

## <a name="next-steps"></a>后续步骤

- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)
- [为属性映射编写表达式](reference-expressions.md)
- [同步的属性](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)