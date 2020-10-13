---
title: Application Insights JavaScript SDK 的本机响应插件
description: 如何安装和使用 Application Insights JavaScript SDK 的本机响应插件。
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 3dac47aa7cf93ca882c4c1d0d191dabf0eb7178c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88227094"
---
# <a name="native-react-plugin-for-application-insights-javascript-sdk"></a>Application Insights JavaScript SDK 的本机响应插件

Application Insights JavaScript SDK 的本机响应插件收集设备信息，默认情况下，此插件会自动收集：

- **唯一的设备 id** (也称为安装 ID。 ) 
- **设备型号名称** (例如 iPhone X、Samsung Galaxy 折页、Huawei P30 Pro 等 ) 
- **设备类型** (例如，话筒、平板电脑等 ) 

## <a name="requirements"></a>要求

必须使用版本 >= 2.0.0 `@microsoft/applicationinsights-web` 。 此插件仅适用于响应本机应用。 它不适用于 [使用展览框架的应用](https://docs.expo.io/)，因此无法使用 Create 反应本机应用。

## <a name="getting-started"></a>入门

安装并链接 [响应本机设备信息](https://www.npmjs.com/package/react-native-device-info) 包。 使包保持最新状态 `react-native-device-info` ，以便使用您的应用程序收集最新的设备名称。

```zsh

npm install --save @microsoft/applicationinsights-react-native @microsoft/applicationinsights-web
npm install --save react-native-device-info
react-native link react-native-device-info

```

## <a name="initializing-the-plugin"></a>正在初始化插件

若要使用此插件，需要构造插件，并将其作为添加 `extension` 到现有 Application Insights 实例。

```typescript
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactNativePlugin } from '@microsoft/applicationinsights-react-native';

var RNPlugin = new ReactNativePlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [RNPlugin]
    }
});
appInsights.loadAppInsights();

```

## <a name="next-steps"></a>后续步骤

- 若要了解有关 JavaScript SDK 的详细信息，请参阅 [Application Insights JAVASCRIPT sdk 文档](javascript.md)。
- 若要了解 Kusto 查询语言和查询 Log Analytics 中的数据，请参阅 [日志查询概述](../../azure-monitor/log-query/log-query-overview.md)。
