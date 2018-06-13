---
title: 自定义远程监视解决方案 UI - Azure | Microsoft Docs
description: 本文提供有关如何访问远程监视解决方案加速器 UI 的源代码以及进行某些自定义的信息。
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/17/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: be20d45b380f66208884f15f4644f36f2a403837
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33774044"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>自定义远程监视解决方案加速器

本文提供有关如何访问远程监视解决方案加速器 UI 的源代码以及对其进行自定义的信息。 本文介绍：

## <a name="prepare-a-local-development-environment-for-the-ui"></a>准备 UI 的本地开发环境

远程监视解决方案加速器 UI 代码是使用 React.js 框架实现的。 可以在 [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) GitHub 存储库中找到源代码。

要对 UI 进行更改，可在本地运行其副本。 本地副本连接到部署的解决方案实例以执行操作（例如检索遥测数据）。

以下步骤概述了为 UI 开发设置本地环境的过程：

1. 使用 **pcs** CLI 部署解决方案加速器的**基本**实例。 记下部署名称以及为虚拟机提供的凭据。 有关详细信息，请参阅[使用 CLI 部署](iot-suite-remote-monitoring-deploy-cli.md)。

1. 使用 Azure 门户或 [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 来启用对解决方案中的微服务所在的虚拟机的 SSH 访问。 例如：

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

1. 使用 Azure 门户或 [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 查找虚拟机的名称和公共 IP 地址。 例如：

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. 使用前一步骤中的 IP 地址以及运行 **pcs** 部署解决方案时提供的凭据，通过 SSH 连接到虚拟机。

1. 若要允许本地 UX 建立连接，请在虚拟机上的 bash shell 中运行以下命令：

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. 看到命令完成并且网站启动后，可从虚拟机断开连接。

1. 在 [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) 存储库的本地副本中，编辑 **.env** 文件以添加部署的解决方案的 URL：

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. 在命令提示符下的 `azure-iot-pcs-remote-monitoring-webui` 文件夹本地副本中，运行以下命令安装所需的库，并在本地运行 UI：

    ```cmd/sh
    npm install
    npm start
    ```

1. 上述命令在本地 (http://localhost:3000/dashboard) 运行 UI。 可以在站点正在运行时编辑代码，并查看其动态更新。

## <a name="customize-the-layout"></a>自定义布局

远程监视解决方案中的每个页面包括一组控件，在源代码中称为“面板”。 例如，“仪表板”页面由五个面板组成：“概述”、“地图”、“警报”、“遥测”和“KPI”。 可以在 [pcs-remote-monitoring-webui](https://github.com/Azure/pcs-remote-monitoring-webui) GitHub 存储库中找到定义每个页面及其面板的源代码。 例如，在 [src/components/pages/dashboard](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) 文件夹中可以找到定义“仪表板”页面、其布局及其面板的代码。

由于面板管理自身的布局和大小，因此你可以轻松修改页面的布局。 例如，对 `src/components/pages/dashboard/dashboard.js` 文件中 **PageContent** 元素进行以下更改会交换地图和遥测面板的位置，并更改地图和 KPI 面板的相对宽度：

```nodejs
<PageContent className="dashboard-container" key="page-content">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={kpisIsPending || devicesIsPending}
        error={devicesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-5">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-4">
    <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlarm={devicesInAlarm}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || kpisIsPending}
          error={azureMapsKeyError || devicesError || kpisError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <KpisPanel
        topAlarms={topAlarmsWithName}
        alarmsPerDeviceId={alarmsPerDeviceType}
        criticalAlarmsChange={criticalAlarmsChange}
        warningAlarmsChange={warningAlarmsChange}
        isPending={kpisIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || kpisError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
  </Grid>
</PageContent>
```

![更改面板布局](media/iot-suite-remote-monitoring-customize/layout.png)

> [!NOTE]
> 地图不是在本地部署中配置。

如果[复制和自定义面板](#duplicate-and-customize-an-existing-control)，则还可以添加同一面板的多个实例或多个版本。 以下示例演示如何通过编辑 `src/components/pages/dashboard/dashboard.js` 文件添加遥测面板的两个实例：

```nodejs
<PageContent className="dashboard-container" key="page-content">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={kpisIsPending || devicesIsPending}
        error={devicesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-2">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-4">
    <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlarm={devicesInAlarm}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || kpisIsPending}
          error={azureMapsKeyError || devicesError || kpisError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <KpisPanel
        topAlarms={topAlarmsWithName}
        alarmsPerDeviceId={alarmsPerDeviceType}
        criticalAlarmsChange={criticalAlarmsChange}
        warningAlarmsChange={warningAlarmsChange}
        isPending={kpisIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || kpisError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
  </Grid>
</PageContent>
```

然后，可以在每个面板中查看不同的遥测数据：

![多个遥测面板](media/iot-suite-remote-monitoring-customize/multiple-telemetry.png)

> [!NOTE]
> 地图不是在本地部署中配置。

## <a name="duplicate-and-customize-an-existing-control"></a>复制和自定义现有控件

以下步骤以“警报”面板为例，概述如何复制现有面板、对其进行修改和使用修改后的版本：

1. 在存储库的本地副本中，复制 `src/components/pages/dashboard/panels` 文件夹中的 **alarms** 文件夹。 将新副本命名为 **cust_alarms**。

1. 在 **cust_alarms** 文件夹中的 **alarmsPanel.js** 文件内，将类名编辑为 **CustAlarmsPanel**：

    ```nodejs
    export class CustAlarmsPanel extends Component {
    ```

1. 将以下代码行添加到 `src/components/pages/dashboard/panels/index.js` 文件：

    ```nodejs
    export * from './cust_alarms';
    ```

1. 在 `src/components/pages/dashboard/dashboard.js` 中将 `AlarmsPanel` 替换为 `CustAlarmsPanel`：

    ```nodejs
    import {
      OverviewPanel,
      CustAlarmsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

现已将原始“警报”面板替换为名为 **CustAlarms** 的副本。 此副本与原始版本相同。 现在可以修改副本。 例如，若要更改“警报”面板中的列顺序：

1. 打开 `src/components/pages/dashboard/panels/cust_alarms/alarmsPanel.js` 文件。

1. 按以下代码片段中所示修改列定义：

    ```nodejs
    this.columnDefs = [
      rulesColumnDefs.severity,
      {
        headerName: 'rules.grid.count',
        field: 'count'
      },
      {
        ...rulesColumnDefs.ruleName,
        minWidth: 200
      },
      rulesColumnDefs.explore
    ];
    ```

以下屏幕截图显示了“警报”面板的新版本：

![已更新“警报”面板](media/iot-suite-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>自定义遥测图表

“仪表板”页面上的遥测图表由 `src/components/pages/dashboard/panels/telemtry` 文件夹中的文件定义。 UI 从 `src/services/telemetryService.js` 文件中的解决方案后端检索遥测数据。 以下步骤说明如何将遥测图表上显示的时间段从 15 分钟更改为 5 分钟：

1. 在 `src/services/telemetryService.js` 文件中，找到名为 **getTelemetryByDeviceIdP15M** 的函数。 复制此函数，并按如下所示修改副本：

    ```nodejs
    static getTelemetryByDeviceIdP5M(devices = []) {
      return TelemetryService.getTelemetryByMessages({
        from: 'NOW-PT5M',
        to: 'NOW',
        order: 'desc',
        devices
      });
    }
    ```

1. 若要使用此新函数填充遥测图表，请打开 `src/components/pages/dashboard/dashboard.js` 文件。 找到初始化遥测流的行，并按如下所示对其进行修改：

    ```node.js
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

现在，遥测图表显示五分钟的遥测数据：

![显示一天数据的遥测图表](media/iot-suite-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>添加新 KPI

“仪表板”页面在“系统 KPI”面板中显示 KPI。 这些 KPI 在 `src/components/pages/dashboard/dashboard.js` 文件中计算。 KPI 由 `src/components/pages/dashboard/panels/kpis/kpisPanel.js` 文件呈现。 以下步骤说明如何在“仪表板”页面上计算和呈现新 KPI 值。 所示的示例在警告警报 KPI 中添加新的百分比更改：

1. 打开 `src/components/pages/dashboard/dashboard.js` 文件。 按如下所示修改 **initialState** 对象，以包含 **warningAlarmsChange** 属性：

    ```nodejs
    const initialState = {
      ...

      // Kpis data
      currentActiveAlarms: [],
      topAlarms: [],
      alarmsPerDeviceId: {},
      criticalAlarmsChange: 0,
      warningAlarmsChange: 0,
      kpisIsPending: true,
      kpisError: null,

      ...
    };
    ```

1. 修改 **currentAlarmsStats** 对象，以包含 **totalWarningCount** 作为属性：

    ```nodejs
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alarmsPerDeviceId: updatedAlarmsPerDeviceId
    };
    ```

1. 计算新 KPI。 找到关键警报计数的计算结果。 复制代码并按如下所示修改副本：

    ```nodejs
    // ================== Warning Alarms Count - START
    const currentWarningAlarms = currentAlarmsStats.totalWarningCount;
    const previousWarningAlarms = previousAlarms.reduce(
      (cnt, { severity }) => severity === 'warning' ? cnt + 1 : cnt,
      0
    );
    const warningAlarmsChange = ((currentWarningAlarms - previousWarningAlarms) / currentWarningAlarms * 100).toFixed(2);
    // ================== Warning Alarms Count - END
    ```

1. 在 KPI 流中包括新的 **warningAlarmsChange** KPI：

    ```nodejs
    return ({
      kpisIsPending: false,

      // Kpis data
      currentActiveAlarms,
      topAlarms,
      criticalAlarmsChange,
      warningAlarmsChange,
      alarmsPerDeviceId: currentAlarmsStats.alarmsPerDeviceId,

      ...
    });

1. Include the new **warningAlarmsChange** KPI in the state data used to render the UI:

    ```nodejs
    const {
      ...

      currentActiveAlarms,
      topAlarms,
      alarmsPerDeviceId,
      criticalAlarmsChange,
      warningAlarmsChange,
      kpisIsPending,
      kpisError,

      ...
    } = this.state;
    ```

1. 更新传递给 KPI 面板的数据：

    ```node.js
    <KpisPanel
      topAlarms={topAlarmsWithName}
      alarmsPerDeviceId={alarmsPerDeviceType}
      criticalAlarmsChange={criticalAlarmsChange}
      warningAlarmsChange={warningAlarmsChange}
      isPending={kpisIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || kpisError}
      colors={chartColorObjects}
      t={t} />
    ```

现已完成 `src/components/pages/dashboard/dashboard.js` 文件中的更改。 以下步骤说明如何在 `src/components/pages/dashboard/panels/kpis/kpisPanel.js` 文件中进行更改，以显示新 KPI：

1. 按如下所示修改以下代码行，以检索新 KPI 值：

    ```nodejs
    const { t, isPending, criticalAlarmsChange, warningAlarmsChange, error } = this.props;
    ```

1. 按如下所示修改标记，以显示新 KPI 值：

    ```nodejs
    <div className="kpi-cell">
      <div className="kpi-header">{t('dashboard.panels.kpis.criticalAlarms')}</div>
      <div className="critical-alarms">
        {
          criticalAlarmsChange !== 0 &&
            <div className="kpi-percentage-container">
              <div className="kpi-value">{ criticalAlarmsChange }</div>
              <div className="kpi-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="kpi-header">{t('Warning alarms')}</div>
      <div className="critical-alarms">
        {
          warningAlarmsChange !== 0 &&
            <div className="kpi-percentage-container">
              <div className="kpi-value">{ warningAlarmsChange }</div>
              <div className="kpi-percentage-sign">%</div>
            </div>
        }
      </div>
    </div>
    ```

“仪表板”页面现在会显示新 KPI 值：

![警告 KPI](media/iot-suite-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>自定义映射

请参阅 GitHub 中的[自定义映射](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map)页，了解解决方案中映射组件的详细信息。

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>其他自定义选项

若要进一步修改远程监视解决方案中的呈现和可视化层，可以编辑代码。 相关的 GitHub 存储库包括：

* [Azure IoT 解决方案的配置微服务 (.NET)](https://github.com/Azure/pcs-ui-config-dotnet/)
* [Azure IoT 解决方案的配置微服务 (Java)](https://github.com/Azure/pcs-ui-config-java/)
* [Azure IoT PCS 远程监视 Web UI](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>后续步骤

本文已介绍可用来帮助自定义远程监视解决方案加速器中 Web UI 的资源。

有关远程监视解决方案加速器的其他概念性信息，请参阅[远程监视体系结构](iot-suite-remote-monitoring-sample-walkthrough.md)

有关自定义远程监视解决方案的详细信息，请参阅[自定义和重新部署微服务](iot-suite-microservices-example.md)
<!-- Next tutorials in the sequence -->