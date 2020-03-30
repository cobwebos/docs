---
title: 在管道活动中使用 Azure Key Vault 机密
description: 了解如何从 Azure 密钥保管库获取存储的凭据，并在数据工厂管道运行期间使用它们。
services: data-factory
author: ChrisLound
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: 09051ad3633ddc720cb34d3d145ccf649fa9cb08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77200106"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>在管道活动中使用 Azure Key Vault 机密

您可以将凭据或机密值存储在 Azure 密钥保管库中，并在管道执行期间使用它们传递到活动。

## <a name="prerequisites"></a>先决条件

此功能依赖于数据工厂托管标识。  了解数据[工厂的托管标识的工作原理，](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)并确保数据工厂具有关联数据。

## <a name="steps"></a>步骤

1. 打开数据工厂的属性并复制托管标识应用程序 ID 值。

    ![托管标识值](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. 打开密钥保管库访问策略，并将托管标识权限添加到获取和列出机密。

    ![Key Vault 访问策略](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Key Vault 访问策略](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    单击"**添加**"，然后单击"**保存**"。

3. 导航到密钥保管库密钥库，并复制机密标识符。

    ![秘密标识符](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    记下在数据工厂管道运行期间要获取的秘密 URI。

4. 在数据工厂管道中，添加新的 Web 活动并将其配置如下。  

    |properties  |“值”  |
    |---------|---------|
    |安全输出     |True         |
    |代码     |[您的机密 URI 值]？api 版本=7.0         |
    |方法     |GET         |
    |身份验证     |MSI         |
    |资源        |https://vault.azure.net       |

    ![Web 活动](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > 您必须在机密 URI 的末尾添加 **？api 版本=7.0。**  

    > [!CAUTION]
    > 将"安全输出"选项设置为 true，以防止以纯文本记录机密值。  使用此值的任何进一步活动都应将其安全输入选项设置为 true。

5. 要在另一个活动中使用该值，请使用以下代码表达式**@activity（"Web1"）。输出.value**。

    ![代码表达式](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>后续步骤

要了解如何使用 Azure 密钥保管库存储数据存储和计算的凭据，请参阅在[Azure 密钥保管库中存储凭据](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
