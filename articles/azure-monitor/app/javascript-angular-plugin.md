---
title: Application Insights JavaScript SDK 的角度插件
description: 如何安装和使用适用于 Application Insights JavaScript SDK 的角插件。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: lagayhar
ms.openlocfilehash: 152ba4b1c8a4e09db0bce759f5b67f577ec5d584
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843765"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Application Insights JavaScript SDK 的角度插件

Application Insights JavaScript SDK 的角度插件启用：

- 跟踪路由器更改
- 角组件使用统计信息

> [!WARNING]
> 角度插件不是 ECMAScript 3 (ES3) 兼容。

## <a name="getting-started"></a>入门

安装 npm 包：

```bash
npm install @microsoft/applicationinsights-angularplugin-js
```

## <a name="basic-usage"></a>基本用法

在应用的条目组件中设置 Application Insights 的实例：

```js
import { Component, OnInit } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin, AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
    private appInsights;
    constructor(
        private router: Router,
        private angularPluginService: AngularPluginService 
    ){
        var angularPlugin = new AngularPlugin();
        this.angularPluginService.init(angularPlugin, this.router);
        this.appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
    }

    ngOnInit() {
        this.appInsights.loadAppInsights();
    }
}

```

若要使用 `trackMetric` 方法跟踪角度组件用法，请在 `AngularPluginService` 文件中的提供程序列表中添加作为提供程序 `app.module.ts` 。

```js
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@NgModule({
    ...
  providers: [ AngularPluginService ],
})
export class AppModule { }
```

若要跟踪组件的生存期，请调用 `trackMetric` `ngOnDestroy` 该组件的方法。 销毁组件时，它将触发一个 `trackMetric` 事件，该事件将发送用户在页面上保持的时间和组件名称。

```js
import { Component, OnDestroy, HostListener } from '@angular/core';
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@Component({
  selector: 'app-test',
  templateUrl: './test.component.html',
  styleUrls: ['./test.component.css']
})
export class TestComponent implements OnDestroy {

  constructor(private angularPluginService: AngularPluginService) {}

  @HostListener('window:beforeunload')
  ngOnDestroy() {
    this.angularPluginService.trackMetric();
  }
}
```

## <a name="next-steps"></a>后续步骤

- 若要了解有关 JavaScript SDK 的详细信息，请参阅 [Application Insights JAVASCRIPT sdk 文档](javascript.md)
- [GitHub 上的角度插件](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)