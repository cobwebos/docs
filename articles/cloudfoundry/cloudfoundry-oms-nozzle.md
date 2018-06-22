---
title: 部署 Azure Log Analytics Nozzle 以监视 Cloud Foundry | Microsoft Docs
description: 有关为 Azure Log Analytics 部署 Cloud Foundry Loggregator Nozzle 的分步指南。 使用 Nozzle 监视 Cloud Foundry 的运行状况和性能指标。
services: virtual-machines-linux
documentationcenter: ''
author: ningk
manager: jeconnoc
editor: ''
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: 687356b60ad0bbc469d67e071ce3bccc8b61ebd7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608995"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>部署 Azure Log Analytics Nozzle 以监视 Cloud Foundry 系统

[Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/) 是 Azure 中的一项服务。 它有助于用户收集并分析云和本地环境生成的数据。

Log Analytics Nozzle（以下简称 Nozzle）是 Cloud Foundry (CF) 组件，负责将指标从 [Cloud Foundry Loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) Firehose 转发到 Log Analytics。 使用 Nozzle，可跨多个部署收集、查看和分析 CF 系统的运行状况和性能指标。

本文档介绍了解如何将 Nozzle 部署到 CF 环境，以及如何在 Log Analytics 控制台中查看数据。

## <a name="prerequisites"></a>先决条件

在部署 Nozzle 之前，必须先完成以下步骤。

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1.在 Azure 中部署 CF 或 Pivotal Cloud Foundry  环境

可在开源 CF 部署或 Pivotal Cloud Foundry (PCF) 部署中使用 Nozzle。

* [在 Azure 上部署 Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [在 Azure 上部署 Pivotal Cloud Foundry](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2.安装用于部署 Nozzle 的 CF 命令行工具

Nozzle 在 CF 环境中以应用程序的形式运行。 需使用 CF CLI 来部署应用程序。

Nozzle 还需要对 Loggregator Firehose 和云控制器拥有访问权限。 若要创建和配置用户，需要用户帐户和身份验证 (UAA) 服务。

* [安装 Cloud Foundry CLI](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [安装 Cloud Foundry UAA 命令行客户端](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

安装 UAA 命令行客户端之前，请务必先安装 Rubygems。

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3.在 Azure 中创建 Log Analytics 工作区

可以手动或使用模板创建 Log Analytics 工作区。 该模板将部署预配置的 OMS KPI 视图和 OMS 控制台警报设置。 

#### <a name="to-create-the-workspace-manually"></a>手动创建工作区：

1. 在 Azure 门户中，在 Azure 市场中搜索服务列表，再选择“Log Analytics”。
2. 选择“创建”，然后为以下各项选择选项：

   * **OMS 工作区**：键入工作区的名称。
   * **订阅**：如果有多个订阅，请选择与 CF 部署相同的订阅。
   * **资源组**：可以新建资源组，也可以使用与 CF 部署相同的资源组。
   * **位置**：输入位置。
   * **定价层**：选择“确定”完成设置。

有关详细信息，请参阅 [Log Analytics 入门](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)。

#### <a name="to-create-the-oms-workspace-through-the-oms-monitoring-template-from-azure-market-place"></a>若要通过 Azure 市场中的 OMS 监测模板创建 OMS 工作区：

1. 打开 Azure 门户。
2. 单击“+”号，或单击左上角的“创建资源”。
3. 在搜索窗口中键入“Cloud Foundry”，选择“OMS Cloud Foundry 监视解决方案”。
4. 将加载 OMS Cloud Foundry 监视解决方案模板前页，单击“创建”以启动模板边栏选项卡。
5. 输入所需的参数：
    * 订阅：选择 OMS 工作区的 Azure 订阅，通常与 Cloud Foundry 部署相同。
    * 资源组：选择现有资源组或为 OMS 工作区创建新资源组。
    * 资源组位置：选择资源组的位置。
    * OMS_Workspace_Name：输入工作区名称，如果工作区不存在，该模板将创建一个新的工作区。
    * OMS_Workspace_Region：选择工作区的位置。
    * **OMS_Workspace_Pricing_Tier**：选择 OMS 工作区 SKU。 有关参考信息，请参阅[定价指南](https://azure.microsoft.com/pricing/details/log-analytics/)。
    * 法律条款：单击法律条款，然后单击“创建”以接受法律条款。
- 指定所有参数后，单击“创建”来部署模板。 完成部署后，状态将显示在通知选项卡处。


## <a name="deploy-the-nozzle"></a>部署 Nozzle

可通过多种不同的方法部署 Nozzle：部署为 PCF 磁贴或 CF 应用程序。

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>将 Nozzle 部署为 PCF Ops Manager 磁贴

请按照[安装和配置 PCF Azure Log Analytics Nozzle](http://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html) 中的步骤操作。这是一种简化的方法，将自动配置 PCF Ops Manager 磁贴并推送 Nozzle。 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>将 Nozzle 手动部署为 CF 应用程序

如果不使用 PCF Ops Manager，可将 Nozzle 部署为应用程序。 以下部分将介绍此过程。

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>通过 CF CLI 以管理员身份登录到 CF 部署

运行以下命令：
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

“SYSTEM_DOMAIN”是 CF 域名。 检索方法为，在 CF 部署清单文件中搜索“SYSTEM_DOMAIN”。 

“CF_User”是 CF 管理员名称。 可以检索名称和密码，具体方法是在 CF 部署清单文件的“scim”部分中搜索名称和“cf_admin_password”。

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>创建 CF 用户并授予所需的权限

运行以下命令：
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

“SYSTEM_DOMAIN”是 CF 域名。 检索方法为，在 CF 部署清单文件中搜索“SYSTEM_DOMAIN”。

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>下载最新的 Log Analytics Nozzle 版本

运行以下命令：
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>设置环境变量。

现在，可在当前目录中的 manifest.yml 文件内设置环境变量。 下面显示了 Nozzle 的应用清单。 请将值替换为特定的 Log Analytics 工作区信息。

```
OMS_WORKSPACE             : Log Analytics workspace ID: open OMS portal from your Log Analytics workspace, select Settings, and select connected sources.
OMS_KEY                   : OMS key: open OMS portal from your Log Analytics workspace, select Settings, and select connected sources.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Log Analytics. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Log Analytics. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Log Analytics. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Log Analytics as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Log Analytics. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>从开发计算机推送应用程序

请务必在 oms-log-analytics-firehose-nozzle 文件夹下操作。 运行以下命令：
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>验证 Nozzle 安装

### <a name="from-apps-manager-for-pcf"></a>通过 Apps Manager（适用于 PCF）

1. 登录到 Ops Manager，确保磁贴显示在安装仪表板上。
2. 登录到 Apps Manager，确保为 Nozzle 创建的空间已在使用情况报告上列出，并确认状态正常。

### <a name="from-your-development-computer"></a>通过开发计算机

在 CF CLI 窗口中，键入：
```
cf apps
```
确保 OMS Nozzle 应用程序正在运行。

## <a name="view-the-data-in-the-oms-portal"></a>在 OMS 门户中查看数据

如果已通过市场模板部署了 OMS 监视解决方案，请转到 Azure 门户，并找到 OMS 解决方案。 可以在模板中指定的资源组中找到解决方案。 单击该解决方案，浏览到“OMS 控制台”，将列出预配置的视图，顶部为 Cloud Foundry 系统 KPI、应用程序数据、警报和 VM 运行状况指标。 

如果已手动创建 OMS 工作区，请执行以下步骤来创建视图和警报：

### <a name="1-import-the-oms-view"></a>1.导入 OMS 视图

在 OMS 门户中，浏览到“视图设计器” > “导入” > “浏览”，选择一个 omsview 文件。 例如，选择“Cloud Foundry.omsview”，并保存该视图。 此时，“概述”页上会显示一个磁贴。 选择此磁贴可查看可视化的指标。

可以通过“视图设计器”自定义这些视图或新建视图。

“Cloud Foundry.omsview”是预览版的 Cloud Foundry OMS 视图模板。 这是一个完全配置的默认模板。 如有模板相关的建议或反馈，请在[问题部分](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues)中发送。

### <a name="2-create-alert-rules"></a>2.创建警报规则

可以[创建警报](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts)，并视需要自定义查询和阈值。 下面是建议的警报：

| 搜索查询                                                                  | 基于以下项生成警报 | 说明                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | 结果数 < 1   | **bbs.Domain.cf-apps** 指示 cf-apps 域是否为最新。 也就是说，来自 Cloud Controller 的 CF 应用程序请求会同步到 bbs.LRPsDesired（Diego 需要的 AI），以供执行。 没有收到数据则表示在指定时间范围内，cf-apps 域不是最新的。 |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | 结果数 > 0   | 对于 Diego 单元，0 表示正常，1 表示不正常。 设置在指定时间范围内检测到多个不正常的 Diego 单元时发出的警报。 |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | 结果数 > 0 | 1 表示系统正常，0 表示系统不正常。 |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | 结果数 > 0   | Consul 定期发出自己的运行状况状态。 0 表示系统正常，1 表示路由发射器检测到 Consul 停止运行。 |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" or Name_s="doppler.shedEnvelopes") Delta_d>0 | 结果数 > 0 | 由于反压力，Doppler 特意降低了消息的增量数。 |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | 结果数 > 0   | Loggregator 发出 **LGR**，指示日志记录进程存在的问题。 例如，日志消息输出过高时，就会出现此类问题。 |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | 结果数 > 0   | 当 Nozzle 从 Loggregator 收到慢使用者警报时，它会向 Log Analytics 发送 slowConsumerAlert ValueMetric。 |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | 结果数 > 0   | 如果丢失的事件增量数达到阈值，表示 Nozzle 存在问题，无法正常运行。 |

## <a name="scale"></a>缩放

可以缩放 Nozzle 和 Loggregator。

### <a name="scale-the-nozzle"></a>缩放 Nozzle

首先应该至少使用两个 Nozzle 实例。 Firehose 跨所有 Nozzle 实例分布工作负荷。
为确保 Nozzle 能够随时掌握 Firehose 的数据流量，请设置前面“创建警报规则”部分中列出的 **slowConsumerAlert** 警报。 看到警报后，请遵循[慢速 Nozzle 指南](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz)确定是否需要进行缩放。
若要纵向扩展 Nozzle，请使用应用程序管理器或 CF CLI，增加 Nozzle 的实例数量或内存或磁盘资源。

### <a name="scale-the-loggregator"></a>缩放 Loggregator

Loggregator 发送 **LGR** 日志消息，指示日志记录进程存在的问题。 可以监视警报，以确定是否需要纵向扩展 Loggregator。
若要纵向扩展 Loggregator，可以增加 Doppler 缓冲区大小，或者在 CF 清单中添加更多的 Doppler 服务器实例。 有关详细信息，请参阅 [Loggregator 缩放指南](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling)。

## <a name="update"></a>更新

若要将 Nozzle 更新为更高版本，请下载新版 Nozzle，遵循前面“部署 Nozzle”部分中的步骤，再次推送应用程序。

### <a name="remove-the-nozzle-from-ops-manager"></a>通过 Ops Manager 删除 Nozzle

1. 登录到 Ops Manager
2. 找到“适用于 PCF 的 Microsoft Azure Log Analytics Nozzle”磁贴。
3. 选择垃圾桶图标并确认删除。

### <a name="remove-the-nozzle-from-your-development-computer"></a>通过开发计算机删除 Nozzle

在 CF CLI 窗口中，键入：
```
cf delete <App Name> -r
```

删除 Nozzle 不会自动删除 OMS 门户中的数据。 它的过期时间取决于 Log Analytics 保留期设置。

## <a name="support-and-feedback"></a>支持和反馈

Azure Log Analytics Nozzle 是开放源代码。 若有问题和反馈，请发送到 [GitHub 部分](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues)。 若要提出 Azure 支持请求，请选择“运行 Cloud Foundry 的虚拟机”作为服务类别。 

## <a name="next-step"></a>后续步骤

自 PCF2.0 起，VM 性能指标将由系统指标转发器传输至 Azure Log Analytics Nozzle，并集成到 OMS 工作区。 你不再需要 OMS 代理来获取 VM 性能指标。 但仍然可以使用 OMS 代理来收集 Syslog 信息。 OMS 代理可作为 Bosh 加载项安装到 CF VM 中。 

有关详细信息，请参阅[将 OMS 代理部署到 Cloud Foundry 部署](https://github.com/Azure/oms-agent-for-linux-boshrelease)。
