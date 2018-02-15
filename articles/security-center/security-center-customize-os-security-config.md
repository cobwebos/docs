---
title: "在 Azure 安全中心自定义 OS 安全配置（预览）| Microsoft Docs"
description: "本文演示如何自定义安全中心评估"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2018
ms.author: terrylan
ms.openlocfilehash: f12441a960db9f1c45bca2a5b95f3669923c7e3d
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
---
# <a name="customize-os-security-configurations-in-azure-security-center-preview"></a>在 Azure 安全中心自定义 OS 安全配置（预览）

本演练演示如何在 Azure 安全中心自定义 OS 安全配置评估。

## <a name="what-are-os-security-configurations"></a>什么是 OS 安全配置？

Azure 安全中心应用 [150 多种建议的规则](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)来监视安全配置。这些规则用于强化 OS，包括与防火墙、审核、密码策略等相关的规则。 如果发现计算机中的某项配置有漏洞，则安全中心会生成安全建议。

通过自定义规则，组织可以控制更适合其环境的配置选项。 可以设置自定义的评估策略并将其应用到订阅中所有适用的计算机。

> [!NOTE]
> - 目前，OS 安全配置自定义仅适用于 Windows Server 版本 2008、2008 R2、2012 和 2012 R2 操作系统。
> - 配置将应用到与选定订阅下的所有工作区相连接的所有 VM 和计算机。
> - 只能在安全中心的标准层使用 OS 安全配置自定义。
>
>

可以通过启用和禁用特定的规则、更改现有规则的所需设置，或基于支持的规则类型（注册表、审核策略和安全策略）添加新的规则，来自定义 OS 安全配置规则。 目前，所需设置必须是确切的值。

新规则的格式和结构必须与现有的其他同类规则相同。

> [!NOTE]
> 若要自定义 OS 安全配置，必须拥有“订阅所有者”、“订阅参与者”或“安全管理员”角色。
>
>

## <a name="customize-the-default-os-security-configuration"></a>自定义默认的 OS 安全配置

若要在安全中心自定义默认的 OS 安全配置，请执行以下操作：

1.  打开“安全中心”仪表板。

2.  在左窗格中，选择“安全策略”。  
    此时将打开“安全中心 - 安全策略”窗口。

    ![安全策略列表](media/security-center-customize-os-security-config/open-security-policy.png)

3.  选择要对其执行自定义的订阅。

4. 在“策略组件”下，选择“编辑安全配置”。  
    此时将打开“编辑安全配置”窗口。

    ![“编辑安全配置”窗口](media/security-center-customize-os-security-config/blade.png)

5. 在右窗格中，遵照步骤下载、编辑和上传修改后的文件。

   > [!NOTE]
   > 默认情况下，下载的配置文件采用 *json* 格式。 有关修改此文件的说明，请转到[自定义配置文件](#customize-the-configuration-file)。
   >

   成功保存文件后，配置将应用到与订阅下的所有工作区相连接的所有 VM 和计算机。 此过程通常需要几分钟或更长时间，具体时间取决于基础结构的大小。

6. 若要提交更改，请选择“保存”。 否则不会存储该策略。

    ![点击“保存”按钮](media/security-center-customize-os-security-config/save-successfully.png)

随时可将当前策略配置重置为默认状态。 为此，请在“编辑 OS 安全配置规则”窗口中选择“重置”。 在确认弹出窗口中选择“是”，确认选择此选项。

![“重置”确认窗口](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>自定义配置文件

在自定义文件中，每个受支持的 OS 版本都有一组规则（规则集）。 每个规则集具有自身的名称和唯一 ID，如以下示例所示：

![规则集名称和 ID](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> 此示例文件是在 Visual Studio 中编辑的，但也可以使用记事本，前提是已安装 JSON 查看器插件。
>
>

编辑自定义文件时，可以修改一条规则或所有规则。 每个规则集包含一个 *rules* 节，该节已划分为三个类别：“注册表”、“审核策略”和“安全策略”，如下所示：

![三个规则集类别](media/security-center-customize-os-security-config/rules-section.png)

每个类别有自身的属性集。 可更改以下属性：

- **expectedValue**：此属性的字段数据类型必须与每种规则类型支持的值匹配，例如：

  - **baselineRegistryRules**：该值应与该规则中定义的 [regValueType](https://msdn.microsoft.com/library/windows/desktop/ms724884) 匹配。

  - **baselineAuditPolicyRules**：使用以下字符串值之一：

    - *Success 和 Failure*

    - *Success*

  - **baselineSecurityPolicyRules**：使用以下字符串值之一：

    - *No one*

    - 允许的用户组列表，例如：*Administrators*, *Backup Operators*

-   **state**：可以包含 *Disabled* 或 *Enabled* 选项的字符串。 在此个人预览版中，该字符串区分大小写。

只能配置这些字段。 如果不符合文件格式或大小，则无法保存更改。 无法处理文件时，将出现以下错误消息：

![安全配置错误消息](media/security-center-customize-os-security-config/invalid-json.png)

有关其他潜在错误的列表，请参阅[错误代码](#error-codes)。

以下三节包含上述规则的示例。 可以更改 *expectedValue* 和 *state* 属性。

**baselineRegistryRules**
```
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\LanManServer\\\\Parameters",
    "valueName": "restrictnullsessaccess",
    "ruleId": "f9020046-6340-451d-9548-3c45d765d06d",
    "originalId": "0f319931-aa36-4313-9320-86311c0fa623",
    "cceId": "CCE-10940-5",
    "ruleName": "Network access: Restrict anonymous access to Named Pipes and
    Shares",
    "ruleType": "Registry",
    "expectedValue": "1",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Disabled"

    }
```

**baselineAuditPolicyRules**
```
    {
    "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
    "ruleId": "37745508-95fb-44ec-ab0f-644ec0b16995",
    "originalId": "2ea0de1a-c71d-46c8-8350-a7dd4d447895",
    "cceId": "CCE-11001-5",
    "ruleName": "Audit Policy: Account Management: Other Account Management Events",
    "ruleType": "AuditPolicy",
    "expectedValue": "Success and Failure",
    "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Enabled"
    }
```

**baselineSecurityPolicyRules**
```
    {
    "sectionName": "Privilege Rights",
    "settingName": "SeIncreaseWorkingSetPrivilege",
    "ruleId": "b0ec9d5e-916f-4356-83aa-c23522102b33",
    "originalId": "b61bd492-74b0-40f3-909d-36b9bf54e94c",
    "cceId": "CCE-10548-6",
    "ruleName": "Increase a process working set",
    "ruleType": "SecurityPolicy",
    "expectedValue": "Administrators, Local Service",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Enabled"
    }
```

不同的 OS 类型存在一些重复的规则。 重复的规则具有相同的 *originalId* 属性。

## <a name="create-custom-rules"></a>创建自定义规则

还可以创建新规则。 在创建新规则之前，请记住以下限制：

-   无法更改架构版本、*baselineId* 和 *baselineName*。

-   无法删除规则集。

-   无法添加规则集。

-   允许的最大规则数（包括默认规则）为 1000 个。

新自定义规则带有新自定义源 (!= "Microsoft") 的标记。 *ruleId* 字段可为 null 或空。 如果为空，Microsoft 会生成一个 ID。 如果不为空，它必须是在所有规则（包括默认规则和自定义规则）中唯一的有效 GUID。 查看核心字段的以下约束：

-   **originalId**：可为 null 或空。 如果 *originalId* 不为空，它应该是有效的 GUID。

-   **cceId**：可为 null 或空。 如果 *cceId* 不为空，它必须是唯一的。

-   **ruleType**：选择下列其中一项：Registry、AuditPolicy 或 SecurityPolicy。

-   **Severity**：选择下列其中一项：Unknown、Critical、Warning 或 Informational。

-   **analyzeOperation**：必须是 *Equals*。

-   **auditPolicyId**：必须是有效的 GUID。

-   **regValueType**：选择下列其中一项：Int、Long、String 或 MultipleString。

> [!NOTE]
> Hive 必须是 *LocalMachine*。
>
>

新自定义规则的示例：

**注册表**：
```
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\Netlogon\\\\MyKeyName",
    "valueName": "MyValueName",
    "originalId": "",
    "cceId": "",
    "ruleName": "My new registry rule”, "baselineRuleType": "Registry",
    "expectedValue": "123", "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "MyCustomSource",
    "state": "Enabled"
    }
```
**安全策略**：
```
   {
   "sectionName": "Privilege Rights",
   "settingName": "SeDenyBatchLogonRight",
   "originalId": "",
   "cceId": "",
   "ruleName": "My new security policy rule", "baselineRuleType":
   "SecurityPolicy",
   "expectedValue": "Guests",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```
**审核策略**：
```
   {
   "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
   "originalId": "",
   "cceId": "",
   "ruleName": " My new audit policy rule ", "baselineRuleType": "AuditPolicy",
   "expectedValue": " Failure",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```

## <a name="file-upload-failures"></a>文件上传失败

如果由于值或格式错误而导致提交的配置文件无效，将会显示失败错误。 可以下载详细的 csv 错误报告以补救和修复错误，然后重新提交更正的配置文件。

![“保存操作失败”错误消息](media/security-center-customize-os-security-config/invalid-configuration.png)

错误文件的示例：

![错误文件示例](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>错误代码

下表列出了所有潜在错误：

| **错误**                                | **说明**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfiguratiohSchemaVersionError  | 发现属性 *schemaVersion* 无效或为空。 该值必须设置为 *{0}*。                                                         |
| BaselineInvalidStringError               | 属性 *{0}* 不能包含 *\\n*。                                                                                                         |
| BaselineNullRuleError                    | 基线配置规则列表包含值为 *null* 的规则。                                                                         |
| BaselineRuleCceIdNotUniqueError          | CCE-ID *{0}* 不是唯一的。                                                                                                                  |
| BaselineRuleEmptyProperty                | 属性 *{0}* 缺失或无效。                                                                                                       |
| BaselineRuleIdNotInDefault               | 该规则包含源属性 *Microsoft*，但未在 Microsoft 默认规则集中找到。                                                   |
| BaselineRuleIdNotUniqueError             | 规则 ID 不是唯一的。                                                                                                                       |
| BaselineRuleInvalidGuid                  | 发现属性 *{0}* 无效。 该值不是有效的 GUID。                                                                             |
| BaselineRuleInvalidHive                  | Hive 必须是 LocalMachine。                                                                                                                   |
| BaselineRuleNameNotUniqueError           | 规则名称不是唯一的。                                                                                                                 |
| BaselineRuleNotExistInConfigError        | 在新配置中找不到该规则。 无法删除规则。                                                                     |
| BaselineRuleNotFoundError                | 在默认基线规则集中找不到该规则。                                                                                        |
| BaselineRuleNotInPlace                   | 该规则与 {0} 类型的默认规则匹配，并已列在 {1} 列表中。                                                                       |
| BaselineRulePropertyTooLong              | 属性 *{0}* 太长。 允许的最大长度: {1}。                                                                                        |
| BaselineRuleRegTypeInvalidError          | 预期值 *{0}* 与定义的注册表值类型不匹配。                                                              |
| BaselineRulesetAdded                     | 在默认配置中找不到 ID 为 *{0}* 的规则集。 无法添加规则集。                                               |
| BaselineRulesetIdMustBeUnique            | 给定的基线规则集 *{0}* 必须是唯一的。                                                                                           |
| BaselineRulesetNotFound                  | 在给定的配置中找不到 ID 为 *{0}* 且名称为 *{1}* 的规则集。 无法删除规则集。                                |
| BaselineRuleSourceNotMatch               | 已定义 ID 为 *{0}* 的规则。                                                                                                       |
| BaselineRuleTypeDoesntMatch              | 默认规则类型为 *{0}*。                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | 规则的实际类型为: *{0}*，但 *ruleType* 属性为 *{1}*。                                                                          |
| BaselineRuleUnpermittedChangesError      | 只允许更改 *expectedValue* 和 *state* 属性。                                                                       |
| BaselineTooManyRules                     | 允许的最大自定义规则数为 {0} 个规则。 给定的配置包含 {1} 个规则，其中包括 {2} 个默认规则，{3} 个自定义规则。 |
| ErrorNoConfigurationStatus               | 找不到配置状态。 请指明所需的配置状态: *Default* 或 *Custom*。                                    |
| ErrorNonEmptyRulesetOnDefault            | 配置状态已设置为默认值。 *BaselineRulesets* 列表必须为 null 或空。                                                          |
| ErrorNullRulesetsPropertyOnCustom        | 给定的配置状态为 *Custom*，但 *baselineRulesets* 属性为 null 或空。                                             |
| ErrorParsingBaselineConfig               | 给定的配置无效。 定义的一个或多个值具有 null 值或无效类型。                                  |
| ErrorParsingIsDefaultProperty            | 给定的 *configurationStatus* 值 *{0}* 无效。 该值只能是 *Default* 或 *Custom*。                                         |
| InCompatibleViewVersion                  | 此工作区类型不支持视图版本 *{0}*。                                                                                   |
| InvalidBaselineConfigurationGeneralError | 在给定的基线配置中发现一个或多个类型验证错误。                                                          |
| ViewConversionError                      | 视图的版本较旧，不受工作区的支持。 视图转换失败: {0}。                                                                 |

如果没有足够的权限，可能会收到常规失败错误，如下所示：

![“保存操作失败”错误消息](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>后续步骤
本文介绍了如何在安全中心自定义 OS 安全配置评估。 若要详细了解配置规则和补救方法，请参阅：

- [安全中心的常见配置标识符和基线规则](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)。
- 安全中心使用通用配置枚举 (CCE) 将唯一标识符分配到配置规则。 有关详细信息，请参阅 [CCE](https://nvd.nist.gov/config/cce/index)。
- [补救安全配置](security-center-remediate-os-vulnerabilities.md)介绍了当 OS 配置与建议的安全配置规则不匹配时如何解决漏洞。
