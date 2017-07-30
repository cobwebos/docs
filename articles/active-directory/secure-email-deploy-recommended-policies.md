---
title: "为安全电子邮件部署建议的策略 | Microsoft Docs"
description: "介绍 Microsoft 就如何部署安全电子邮件策略和配置提供的建议和核心概念。"
author: jeffgilb
manager: femila
editor: jsnow
ms.service: guidance
ms.topic: article
ms.date: 07/12/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
pnp.series.title: Best Practices
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: a5eb77c68c48e9cca2805fa64075279560179e14
ms.contentlocale: zh-cn
ms.lasthandoff: 07/15/2017

---

# <a name="deploy-recommended-secure-email-policies"></a>部署建议的安全电子邮件策略

本部分介绍如何在新预配的环境中部署建议的策略。 在单独的实验室环境中设置这些策略即可了解和评估建议的策略，然后将内容分阶段推出到预生产环境和生产环境。 新预配的环境可以是仅限云的环境，也可以是混合环境。  

若要成功部署建议的策略，需在 Azure 门户中执行操作，以满足前述先决条件。 具体而言，需要：
* 配置命名的网络，确保 Azure Identity Protection 能够正常地生成风险评分
* 要求所有用户注册多重身份验证 (MFA)
* 配置密码同步和自助服务密码重置，使用户能够自行重置密码

可以让 Azure AD 和 Intune 的策略都以特定的用户组为目标。 建议以分阶段方式推出此前定义的策略。 这样即可通过增量方式验证策略的执行效果，以及与策略相关的支持团队的表现。

## <a name="baseline-conditional-access-policy"></a>基线条件性访问策略

若要创建新的条件性访问策略，请使用管理员凭据登录到 Microsoft Azure 门户。 然后导航到“Azure Active Directory”>“安全性”>“条件性访问”。 

可以添加新的策略（+添加），如以下屏幕截图所示：

![基线 CA 策略](./media/secure-email/baseline-ca-policy.png)

下表介绍表述策略时所需的适当设置，这些策略是进行每个级别的保护所必需的。

### <a name="medium-and-above-risk-requires-mfa"></a>中等及中等以上风险需要 MFA

下表介绍要针对此策略实施的条件性访问策略设置。

|Categories|类型|属性|值|说明|
|:---------|:---|:---------|:-----|:----|
|分配|用户和组|包括|选择用户和组 – 选择包含目标用户的特定安全组|一开始请使用包括试点用户在内的安全组。|
|||排除|异常安全组；服务帐户（应用标识）|根据需要临时更改的成员身份|
||云应用|包括|选择应用 - 选择 Office 365 Exchange Online||
||条件|已配置|是|根据环境和需求进行配置|
||登录风险|风险级别|高、中|二者都选中|
|访问控制|授权|授予访问权限|True|选定|
|||要求 MFA|True|勾选标记|
|||要求符合的设备|False||
|||要求已加入域的设备|False||
|||要求所有选定控件|True|选定|
|启用策略|||启用|部署条件性访问策略|

### <a name="require-a-compliant-or-domain-joined-device"></a>需要符合的或已加入域的设备

若要为 Exchange Online 创建新的 Intune 条件性访问策略，请使用管理员凭据登录到 [Microsoft 管理门户 (http://manage.microsoft.com)](http://manage.microsoft.com/)，然后导航到“策略”>“条件性访问”>“Exchange Online 策略”。

![Exchange Online 策略](./media/secure-email/exchange-online-policy.png)

必须在 Intune 管理门户中专为 Exchange Online 设置条件性访问策略，然后才能要求符合的或已加入域的设备。

|Categories|类型|属性|值|说明|
|:---------|:---|:---------|:-----|:----|
|**应用程序访问**|Outlook 和其他使用新式身份验证的应用|所有平台|True|选定|
|||Windows 必须满足以下要求|设备必须已加入域或合规|选定（列表）|
|||选定平台|False||
||Outlook Web Access (OWA)|在 Outlook 所在的平台上阻止不符合的设备|True|勾选标记|
||使用基本身份验证的 Exchange ActiveSync 应用|在 Microsoft Intune 所支持的平台上阻止不符合的设备|True|勾选标记|
|||在 Microsoft Intune 不支持的平台上阻止所有其他设备|True|勾选标记|
|策略部署|目标组|选择此策略所针对的 Active Directory 组|||
|||所有用户|False||
|||选定安全组|True|选定|
|||修改|选择包含目标用户的特定安全组||
||免除组|选择要从此策略中免除的 Active Directory 组（替代“目标组”列表的成员）|||
|||无免除用户|True|选定|
|||选定安全组|False|||

### <a name="mobile-application-management-conditional-access-for-exchange-online"></a>针对 Exchange Online 的移动应用程序管理条件性访问

必须在 Intune 管理门户中专为 Exchange Online 设置条件性访问策略，然后才能管理移动应用。

若要管理移动应用，请使用管理员凭据登录到 Microsoft Azure 门户，然后导航到“Intune 应用保护”>“设置”>“条件性访问”>“Exchange Online”。

|Categories|类型|属性|值|说明|
|:---------|:---|:---------|:-----|:----|
|应用访问|允许的应用|启用应用访问|允许支持 Intune 应用策略的应用|选定（列表）– 这会生成一个列表，其中包含 Intune 应用策略所支持的应用/平台组合|
|用户访问|允许的应用|受限的用户组|添加用户组 – 选择包含目标用户的特定安全组|一开始请使用包括试点用户在内的安全组|
|||免除用户组|例外安全组|||

#### <a name="apply-to"></a>应用于

试点项目完成后，即应将这些策略应用于组织中的所有用户。

## <a name="sensitive-conditional-access-policy"></a>敏感的条件性访问策略

### <a name="low-and-above-risk-requires-mfa"></a>低风险及以上需要 MFA
下表介绍要针对低风险及以上策略实施的条件性访问策略设置。

|Categories|类型|属性|值|说明|
|:---------|:---|:---------|:-----|:----|
|分配|用户和组|包括|选择用户和组 – 选择包含目标用户的特定安全组|一开始请使用包括试点用户在内的安全组|
|||排除|异常安全组；服务帐户（应用标识）|根据需要临时更改的成员身份|
||云应用|包括|选择应用 - 选择 Office 365 Exchange Online||
||条件|已配置|是|根据环境和需求进行配置|
||登录风险|已配置|是|根据环境和需求进行配置|
|||风险级别|低、中、高|选中所有三项|
|访问控制|授权|授予访问权限|True|选定|
|||要求 MFA|True|勾选标记|
|||要求符合的设备|False||
|||要求已加入域的设备|False||
|||要求所有选定控件|True|选定|
|启用策略|||启用|部署条件性访问策略|

### <a name="require-a-compliant-or-domain-joined-device"></a>需要符合的或已加入域的设备
（请参阅基线说明）

### <a name="mobile-application-management-conditional-access-for-exchange-online"></a>针对 Exchange Online 的移动应用程序管理条件性访问

（请参阅基线说明）

#### <a name="apply-to"></a>应用于

完成试点项目后，即应将这些策略应用到组织中需要访问被视为敏感内容的电子邮件的用户。

## <a name="highly-regulated-conditional-access-policy"></a>高度管控的条件性访问策略
### <a name="mfa-required"></a>需要 MFA

下表介绍要针对高度管控策略实施的条件性访问策略设置。

|Categories|类型|属性|值|说明|
|:---------|:---|:---------|:-----|:----|
|分配|用户和组|包括|选择用户和组 – 选择包含目标用户的特定安全组|一开始请使用包括试点用户在内的安全组|
|||排除|异常安全组；服务帐户（应用标识）|根据需要临时更改的成员身份|
||云应用|包括|选择应用 - 选择 Office 365 Exchange Online||
|访问控制|授权|授予访问权限|True|选定|
|||要求 MFA|True|勾选标记|
|||要求符合的设备|False|勾选标记|
|||要求已加入域的设备|False||
|||要求所有选定控件|True|选定|
|启用策略|||启用|部署条件性访问策略|

### <a name="require-a-compliant-or-domain-joined-device"></a>需要符合的或已加入域的设备
（请参阅基线说明）
### <a name="mobile-application-management-conditional-access-for-exchange-online"></a>针对 Exchange Online 的移动应用程序管理条件性访问
（请参阅基线说明）
#### <a name="apply-to"></a>应用于
完成试点项目后，即应将这些策略应用到组织中需要访问被视为高度管控内容的电子邮件的用户。

## <a name="user-risk-policy"></a>用户风险策略
### <a name="high-risk-users-must-change-password"></a>高风险用户必须更改密码
若要确保所有高风险用户的已泄露帐户在登录时都强制执行密码更改，必须应用以下策略。 

使用管理员凭据登录到 [Microsoft Azure 门户 (http://portal.azure.com)](http://portal.azure.com/)，然后导航到“Azure AD Identity Protection”>“用户风险策略”。

|Categories|类型|属性|值|说明|
|:---------|:---|:---------|:-----|:----|
|分配|用户|包括|所有用户|选定|
|||排除|无||
||条件|用户风险|高|选定|
|控制|Access|允许访问|True|选定|
||Access|要求更改密码|True|勾选标记|
|**评审**|不适用|不适用|不适用|不适用|
|强制实施策略|||启用|启动“强制实施策略”|

## <a name="additional-configurations"></a>其他配置
除了上述策略，还必须配置下面的“移动应用程序和设备管理”设置，详见本部分的介绍。 

### <a name="intune-mobile-application-management"></a>Intune 移动应用程序管理 

若要确保针对每个安全和数据保护层的前述策略建议能够保护电子邮件，必须在 Azure 门户中创建 Intune 应用保护策略。

若要创建新的应用保护策略，请使用管理员凭据登录到 Microsoft Azure 门户，然后导航到“Intune 应用保护”>“设置”>“应用策略”。

添加新的策略（+添加），如以下屏幕截图所示：

![Intune 移动应用程序管理](./media/secure-email/intune-mobile-app-mgmt.png)

>[!NOTE]
>iOS 和 Android 的应用保护策略选项略有不同。 下面的策略专用于 Android。
>

下表详细介绍表述策略时所需的适当设置，这些策略是进行每个级别的保护所必需的。
| 下表介绍建议的 Intune 应用保护策略设置。

|Categories|类型|属性|值|说明|
|:---------|:---|:---------|:-----|:----|
|**常规**|电子邮件|名称|适用于 Android 的安全电子邮件策略|输入策略名称|
|||说明||输入描述策略的文本|
|||平台|Android|iOS 和 Android 的应用保护策略选项略有不同；此策略专用于 Android|
|应用|应用程序|应用|Outlook|选定（列表）|
|**设置**|数据重定位|阻止 Android 备份|是|在 iOS 上，这会专门调用 iTunes 和 iCloud|
||||允许应用将数据传输到其他应用|策略管理的应用||
|||允许应用接收到其他应用的数据|策略管理的应用||
|||阻止“另存为”|是||
|||限制与其他应用进行剪切、复制和粘贴|策略管理的应用||
|||限制 Web 内容在 Managed Browser 中显示|否||
|||加密应用数据|是|在 iOS 上选择选项：当设备锁定时|
|||禁用联系人同步|否||
||Access|要求使用 PIN 进行访问|是||
|||PIN 重置之前的尝试次数|3||
|||允许简单的 PIN|否||
|||PIN 长度|6||
|||允许使用指纹来代替 PIN|是||
|||要求使用公司凭据进行访问|否||
|||阻止托管应用在已越狱或取得 root 权限的设备上运行|是||
|||以下时间过后重新检查访问要求(分钟)|30||
|||脱机宽限期|720||
|||擦除应用数据之前的脱机间隔时间(天数)|90||
|||阻止屏幕捕获和 Android 助手|否|在 iOS 上，这不是可用选项|

完成后，请记住单击“创建”。 重复上述步骤，将所选平台（下拉列表）替换为 iOS。 这样会创建两个应用策略，因此在创建策略后就应将组分配给策略，然后部署策略。

### <a name="intune-mobile-device-management"></a>Intune 移动设备管理
使用管理员凭据登录到 [Microsoft 管理门户 (http://manage.microsoft.com)](https://manage.microsoft.com/) 即可创建以下“配置和符合性”策略。

#### <a name="ios-email-profile"></a>iOS 电子邮件配置文件
在 [Intune 管理门户 (https://manage.microsoft.com)](https://manage.microsoft.com/) 中转到“策略”>“配置策略”>“添加”>“iOS”>“电子邮件配置文件(iOS 8 及更高版本)”，创建以下配置策略。

|Categories|类型|属性|值|说明|
|:---------|:---|:---------|:-----|:----|
|电子邮件配置文件|Exchange ActiveSync|主机 (#)|Outlook.office365.com||
|||帐户名 (#)|SecureEmailAccount|管理员选择|
|||用户名|用户主体名称|选定 – 下拉列表|
|||电子邮件地址|主 SMTP 地址|选定 – 下拉列表|
|||身份验证方法|用户名和密码|选定 – 下拉列表|
|||使用 S/MIME|False||
||同步设置|距离电子邮件同步还剩的天数|两周|选定 – 下拉列表|
|||使用 SSL|True|勾选标记|
|||允许将邮件移到其他电子邮件帐户|False||
|||允许从第三方应用程序发送电子邮件|True||
|||同步最近使用的电子邮件地址|True|勾选标记|

#### <a name="ios-app-sharing-profile"></a>共享配置文件的 iOS 应用
在 [Intune 管理门户 (https://manage.microsoft.com)](https://manage.microsoft.com/) 中转到“策略”>“配置策略”>“添加”>“iOS”>“常规配置(iOS 8.0 及更高版本)”，创建以下配置策略。

|Categories|类型|属性|值|说明|
|:---------|:---|:---------|:-----|:----|
|**安全性**|全部|全部|未配置||
|**云**|全部|全部|未配置||
|**应用程序**|浏览器|全部|未配置||
||应用|允许安装应用|未配置||
|||要求使用密码来访问应用程序商店|未配置||
|||所有应用内购买|未配置||
|||允许将托管文档置于其他托管应用（iOS 8.0 及更高版本）中|否|选定 – 下拉列表|
|||允许将非托管文档置于其他托管应用中|未配置||
|||允许视频会议|未配置||
|||允许用户信任新的企业应用作者|未配置||
||游戏|全部|未配置||
||媒体内容|全部|未配置|||

#### <a name="android-email-profile"></a>Android 电子邮件配置文件
在 [Intune 管理门户 (https://manage.microsoft.com)](https://manage.microsoft.com/) 中转到“策略”>“配置策略”>“添加”>“iOS”>“电子邮件配置文件(Samsung KNOX Standard 4.0 及更高版本)”，创建以下配置策略。

|Categories|类型|属性|值|说明|
|:---------|:---|:---------|:-----|:----|
|电子邮件配置文件|Exchange ActiveSync|主机 (#)| Outlook.office365.com|
|||帐户名 (#)|SecureEmailAccount|管理员选择|
|||用户名|用户主体名称|选定 – 下拉列表|
|||电子邮件地址|主 SMTP 地址|选定 – 下拉列表|
|||身份验证方法|用户名和密码|选定 – 下拉列表|
|||使用 S/MIME|False||
||同步设置|距离电子邮件同步还剩的天数|两周|选定 – 下拉列表|
|||同步计划|未配置|选定 – 下拉列表|
|||使用 SSL|True|勾选标记|
|||要同步的内容类型|||
|||电子邮件|True|勾选（锁定）|
|||联系人|True|勾选标记|
|||日历|True|勾选标记|
|||任务|True|勾选标记|
|||说明|True|勾选标记|

#### <a name="android-for-work-email-profile"></a>Android for Work 电子邮件配置文件
在 [Intune 管理门户 (https://manage.microsoft.com)](https://manage.microsoft.com/) 中转到“策略”>“配置策略”>“添加”>“iOS”>“电子邮件配置文件(Android for Work - Gmail)”，创建以下配置策略。

|Categories|类型|属性|值|说明|
|:---------|:---|:---------|:-----|:----|
|电子邮件配置文件|Exchange ActiveSync|主机(#)| Outlook.office365.com|
|||帐户名(#)|SecureEmailAccount|管理员选择|
|||用户名|用户主体名称|选定 – 下拉列表|
|||电子邮件地址|主 SMTP 地址|选定 – 下拉列表|
|||身份验证方法|用户名和密码|选定 – 下拉列表|
||同步设置|距离电子邮件同步还剩的天数|两周|选定 – 下拉列表|
|||使用 SSL|True|勾选标记|

#### <a name="android-for-work-app-sharing-profile"></a>Android for Work 应用共享配置文件
在 [Intune 管理门户 (https://manage.microsoft.com)](https://manage.microsoft.com/) 中转到“策略”>“配置策略”>“添加”>“iOS”>“常规配置(Android for Work)”，创建以下配置策略。

|Categories|类型|属性|值|说明|
|:---------|:---|:---------|:-----|:----|
|**安全性**|密码|最短密码长度|未配置||
|||在删除工作配置文件之前的重复登录失败次数|未配置||
|||在设备锁定之前处于非活动状态的分钟数|未配置||
|||密码过期(天)|未配置||
|||记住密码历史记录|未配置||
|||要求使用密码来解锁移动设备|未配置||
|||允许指纹解锁(Android 6.0+)|未配置||
|||允许 Smart Lock 和其他信任代理(Android 6.0+)|未配置||
||工作配置文件设置|允许在工作配置文件和个人配置文件之间共享数据|工作配置文件中的应用可以处理来自个人配置文件的共享请求|选定 – 下拉列表|
|||当设备锁定时隐藏工作配置文件通知(Android 6.0+)|未配置||
|||设置默认的应用权限策略(Android 6.0+)|未配置|||

#### <a name="device-compliance-policy"></a>设备符合性策略
在 [Intune 管理门户 (https://manage.microsoft.com)](https://manage.microsoft.com/) 中转到“策略”>“符合性策略”>“添加”，创建以下配置策略。

|Categories|类型|属性|值|说明|
|:---------|:---|:---------|:-----|:----|
|系统安全|密码|要求使用密码来解锁移动设备(...)|是|选定 – 下拉列表|
|||允许简单的密码(...)|否|选定 – 下拉列表|
|||最短密码长度(...)|6|选定 – 列表|
||高级密码设置|全部|未配置||
||加密|要求对移动设备加密(...)|是|选定 – 下拉列表|
||电子邮件配置文件|电子邮件帐户必须由 Intune 管理(iOS 8.0+)|是| 选定 – 下拉列表|
|||选择(#)||必须选择适用于 iOS 的电子邮件配置策略：iOS 电子邮件策略（参见上述配置策略）|
|设备运行状况|Windows 确定运行状况证明|要求设备运行状况报告为正常(Windows 10 桌面版、移动版及更高版本)|是||
||设备安全设置|全部|未配置||
||设备威胁防护|全部|未配置||
||越狱|设备不得越狱或取得 root 权限(iOS 8.0+、Android 4.0+)|是||
|设备属性|操作系统版本|全部|未配置|||

所有上述策略必须以用户组为目标，才会被系统视为已部署。 为此，可以通过单击“保存”来创建策略，也可以稍后在“策略”部分（与“添加”处于同一层级）选择“管理部署”。

## <a name="remediating-events-that-have-results-in-medium-or-high-risk-access"></a>修正已导致中等风险或高风险访问的事件
如果某个用户报告其现在需要执行以前不需要的 MFA，则支持部门可以从风险角度查看用户状态。  

组织中充当全局管理员或安全管理员角色的用户可以使用 Azure AD Identity Protection 来查看对计得的风险评分有影响的风险事件。 如果此类用户发现某些事件被标记为可疑，但经确认为有效事件（例如，某位员工在度假时从陌生的位置登录），则管理员可以解决该事件，使之不再影响风险评分。

## <a name="next-steps"></a>后续步骤
[详细了解 Office 365 和 EMS 服务](secure-email-ems-office365-service-descriptions.md)

