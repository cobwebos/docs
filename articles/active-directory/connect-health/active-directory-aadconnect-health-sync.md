---
title: "使用 Azure AD Connect Health 进行同步 | Microsoft 文档"
description: "本页与 Azure AD Connect Health 相关，介绍如何监视 Azure AD Connect 同步。"
services: active-directory
documentationcenter: 
author: karavar
manager: mtillman
ms.assetid: 1dfbeaba-bda2-4f68-ac89-1dbfaf5b4015
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 821d4bd7e6b526ad826caf005456edf8235291b8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="monitor-azure-ad-connect-sync-with-azure-ad-connect-health"></a>使用 Azure AD Connect Health 监视 Azure AD Connect 同步
以下文档专门介绍如何使用 Azure AD Connect Health 来监视 Azure AD Connect (Sync)。  有关使用 Azure AD Connect Health 监视 AD FS 的信息，请参阅 [在 AD FS 中使用 Azure AD Connect Health](active-directory-aadconnect-health-adfs.md)。 此外，有关使用 Azure AD Connect Health 监视 Active Directory 域服务的信息，请参阅 [在 AD DS 中使用 Azure AD Connect Health](active-directory-aadconnect-health-adds.md)。

![用于同步的 Azure AD Connect Health](./media/active-directory-aadconnect-health-sync/sync-blade.png)

## <a name="alerts-for-azure-ad-connect-health-for-sync"></a>用于同步的 Azure AD Connect Health 的警报
用于同步的 Azure AD Connect Health 警报部分将提供活动警报列表。 每个警报均包含相关信息、解决方法步骤和相关文档的链接。 选择活动或已解决的警报后，将看到一个新的边栏选项卡，其中会显示额外信息、可用于解决警报的方法步骤以及其他文档的链接。 还可以查看过去已解决警报的相关历史数据。

选择警报后，将获取到额外信息、可用于解决警报的方法步骤以及其他文档的链接。

![Azure AD Connect 同步错误](./media/active-directory-aadconnect-health-sync/alert.png)

### <a name="limited-evaluation-of-alerts"></a>警报的受限评估
如果 Azure AD Connect 未使用默认配置（例如，如果属性筛选已从默认配置更改为自定义配置），则 Azure AD Connect Health 代理将不会上传与 Azure AD Connect 相关的错误事件。

这会限制服务对警报进行评估。 将在服务的 Azure 门户中看到一条指示这种状态的横幅消息。

![用于同步的 Azure AD Connect Health](./media/active-directory-aadconnect-health-sync/banner.png)

可以通过单击“设置”并允许 Azure AD Connect Health 代理上传所有错误日志，来改变这种状态。

![用于同步的 Azure AD Connect Health](./media/active-directory-aadconnect-health-sync/banner2.png)

## <a name="sync-insight"></a>深入了解同步
管理员往往想要了解将更改同步到 Azure AD 所花费的时间以及发生的更改量。 借助此功能，可以使用以下图形轻松可视化这些信息：   

* 同步操作延迟
* 对象更改趋势

### <a name="sync-latency"></a>同步延迟
此功能提供连接器同步操作（导入、导出等）延迟的图形趋势。  这提供了一种快速方便的方式，使我们不仅可以了解操作延迟（发生大量更改时延迟较大），而且还可以检测导致延迟的、可能需要进一步调查的异常行为。

![同步延迟](./media/active-directory-aadconnect-health-sync/synclatency02.png)

默认情况下只显示 Azure AD 连接器“导出”操作的延迟。  若要查看对连接器执行的其他操作或其他连接器执行的操作，请右键单击图表，选择“编辑图表”，或者单击“编辑延迟图表”并选择特定的操作和连接器。

### <a name="sync-object-changes"></a>同步对象更改
此功能提供正在评估并导出到 Azure AD 的更改数的图形趋势。  目前，尝试从同步日志收集此信息并不容易。  图表不仅可让你以更简单的方式监视环境中发生的更改数，而且可以提供正在发生的失败的可视化视图。

![同步延迟](./media/active-directory-aadconnect-health-sync/syncobjectchanges02.png)

## <a name="object-level-synchronization-error-report-preview"></a>对象级同步错误报告（预览版）
此功能提供有关使用 Azure AD Connect 在 Windows Server AD 与 Azure AD 之间同步标识数据时可能发生的同步错误的报告。

* 该报告涵盖同步客户端（Azure AD Connect 1.1.281.0 或更高版本）记录的错误
* 其中包含上一次在同步引擎中执行同步操作时发生的错误。 （Azure AD 连接器中的“导出”。）
* 为了在报告中包含最新的数据，用于同步的 Azure AD Connect Health 代理必须与所需的终结点建立出站连接。
* 系统会使用用于同步的 Azure AD Connect Health 代理上传的数据每隔 30 分钟更新该报告。该报告提供以下重要功能

  * 错误分类
  * 按类别列出出错的对象
  * 在一个位置显示有关错误的所有数据
  * 并列比较由于冲突而出错的对象
  * 以 CSV 格式下载错误报告（即将推出）

### <a name="categorization-of-errors"></a>错误分类
该报告将现有同步错误划分为以下类别：

| 类别 | 说明 |
| --- | --- |
| 重复属性 |当 Azure AD Connect 在 Azure AD 中尝试创建或更新对象时出错，因为这些对象具有一个或多个属性的重复值，但这些值在 proxyAddresses、UserPrincipalName 等租户中必须唯一。 |
| 数据不匹配 |当软匹配无法匹配导致同步错误的对象时出错。 |
| 数据验证失败 |由于数据无效（例如，UserPrincipalName 等关键属性中包含不受支持的字符，写入 Azure AD 之前使验证失败的格式错误）而出错。 |
| 属性过大 |由于一个或多个属性大于允许的大小、长度或计数而出错。 |
| 其他 |不适合上述类别的所有其他错误。 我们会根据反馈将此类别细分为若干子类别。 |

![同步错误报告摘要](./media/active-directory-aadconnect-health-sync/errorreport01.png)
![同步错误报告类别](./media/active-directory-aadconnect-health-sync/errorreport02.png)

### <a name="list-of-objects-with-error-per-category"></a>按类别列出出错的对象
深入到每个类别可以查看该类别中出错的对象列表。
![同步错误报告列表](./media/active-directory-aadconnect-health-sync/errorreport03.png)

### <a name="error-details"></a>错误详细信息
每种错误的详细视图中提供以下数据

* 相关 *AD 对象*的标识符
* 相关 *Azure AD 对象*的标识符（如果适用）
* 错误说明和解决方法
* 相关文章

![同步错误报告详细信息](./media/active-directory-aadconnect-health-sync/errorreport04.png)

### <a name="download-the-error-report-as-csv"></a>以 CSV 格式下载错误报告
选择“导出”按钮即可下载一个 CSV 文件，其中包含所有错误的所有详细信息。

## <a name="related-links"></a>相关链接
* [排查同步期间的错误](../connect/active-directory-aadconnect-troubleshoot-sync-errors.md)
* [重复属性复原](../connect/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health 代理安装](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health 操作](active-directory-aadconnect-health-operations.md)
* [在 AD FS 中使用 Azure AD Connect Health](active-directory-aadconnect-health-adfs.md)
* [在 AD DS 中使用 Azure AD Connect Health](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health 常见问题](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health 版本历史记录](active-directory-aadconnect-health-version-history.md)