---
title: 粗 Relocalization
description: 粗 relocalization 快速入门指南。
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3477bac051346e4b334ff3437085c402090b2c98
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765455"
---
# <a name="coarse-relocalization"></a>粗略重新局部化

粗 relocalization 是提供问题初始答案的一项功能：我的*设备现在的位置/我应该观察哪些内容？* 响应并不精确，但其形式如下：*你接近这些定位点，尝试找到其中一个*。

通过将各种设备上的传感器读数与创建和查询定位点相关联，来 relocalization。 对于户外方案，传感器数据通常是设备的 GPS （全球定位系统）位置。 当 GPS 不可用或不可靠时（例如室内），传感器数据将包含在 WiFi 接入点和蓝牙信标范围内。 所有收集的传感器数据都有助于维护空间索引。 定位点服务利用空间索引快速确定设备约100米以内的定位点。

通过粗糙 relocalization 启用的定位点的快速查找可简化应用程序的开发，这些应用程序是由全球规模的集合（假设为数百万个异地分散的）定位点支持的。 定位点管理的复杂性全部处于隐藏状态，从而使你可以更多地关注你的出色应用程序逻辑。 所有锚点都由服务在幕后完成：

## <a name="collected-sensor-data"></a>收集的传感器数据

可以发送到锚定服务的传感器数据如下：

* GPS 位置：纬度、经度、海拔高度。
* 范围内 WiFi 接入点的信号强度。
* 范围内蓝牙信标的信号强度。

通常，应用程序需要获取特定于设备的权限才能访问 GPS、WiFi 或 BLE 数据。 此外，某些平台上的设计不提供上述某些传感器数据。 为了应对这些情况，传感器数据的收集是可选的，默认情况下处于关闭状态。

## <a name="set-up-the-sensor-data-collection"></a>设置传感器数据收集

首先，让我们创建一个传感器指纹提供程序，并使该会话知道它：

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Create and configure the session
cloudSpatialAnchorSession = [[ASACloudSpatialAnchorSession alloc] init];

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Create and configure the session
cloudSpatialAnchorSession = ASACloudSpatialAnchorSession()

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Create and configure the session
cloudSpatialAnchorSession = std::make_shared<CloudSpatialAnchorSession>();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)
```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);
```
---

接下来，需要确定要用于粗糙 relocalization 的传感器。 通常，此决定是特定于正在开发的应用程序的，但下表中的建议应能为您带来很好的起点：


|             | 室内 | 室外 |
|-------------|---------|----------|
| GPS         | 关闭 | 开 |
| WiFi        | 开 | 启用（可选） |
| BLE 信标 | 开启（可选，请参阅下文） | 关闭 |


### <a name="enabling-gps"></a>启用 GPS

假设你的应用程序已具有访问设备 GPS 位置的权限，则可以配置 Azure 空间锚点来使用它：

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);
```

---

在应用程序中使用 GPS 时，请记住，硬件提供的读数通常是：

* 异步和低频率（小于 1 Hz）。
* 不可靠/干扰性（平均 7-m 标准偏差）。

通常情况下，设备 OS 和 Azure 空间锚，会对原始 GPS 信号进行一些筛选和推断，以尝试缓解这些问题。 这一额外处理需要额外的时间来实现聚合，因此，为获得最佳结果，应尝试执行以下操作：

* 在应用程序中尽快创建传感器指纹提供程序
* 使传感器指纹提供程序处于活动状态并在多个会话之间共享

如果计划在定位点会话之外使用传感器指纹提供程序，请确保在请求传感器估算之前启动该提供程序。 例如，以下代码将负责实时更新地图上设备的位置：

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(
        x: geoPose.Longitude,
        y: geoPose.Latitude,
        radius: geoPose.HorizontalError);
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
// Game about to start, start tracking the sensors
[sensorProvider start];

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    ASAGeoLocation *geoPose = [sensorProvider getLocationEstimate];

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError);
}

// Game ended, no need to track the sensors anymore
[sensorProvider stop];
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
// Game about to start, start tracking the sensors
sensorProvider?.start()

// Game loop
while m_isRunning
{
    // Get the GPS estimate
    var geoPose: ASAGeoLocation?
    geoPose = sensorProvider?.getLocationEstimate()

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError)
}

// Game ended, no need to track the sensors anymore
sensorProvider?.stop()
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
// Game about to start, start tracking the sensors
sensorProvider.start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.getLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.getLongitude(), geoPose.getLatitude(), geoPose.getHorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.stop();
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
// Game about to start, start tracking the sensors
sensorProvider->Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    std::shared_ptr<GeoLocation> geoPose = sensorProvider->GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose->Longitude(), geoPose->Latitude(), geoPose->HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider->Stop();
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.Longitude(), geoPose.Latitude(), geoPose.HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

---

### <a name="enabling-wifi"></a>启用 WiFi

假设你的应用程序已具有访问设备的 WiFi 状态的权限，则可以配置 Azure 空间锚点来使用它：

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.wifiEnabled = true;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.wifiEnabled = true
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setWifiEnabled(true);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->WifiEnabled(true);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.WifiEnabled(true);
```

---

在应用程序中使用 WiFi 时，请记住，硬件提供的读数通常是：

* 异步和低频率（小于 0.1 Hz）。
* 在 OS 级别可能会受到限制。
* 不可靠/干扰（平均为 3-dBm 标准偏差）。

Azure 空间锚点将在会话期间尝试生成经过筛选的 WiFi 信号强度映射，以尝试缓解这些问题。 为获得最佳结果，应尝试执行以下操作：

* 在放置第一个定位点之前，请先创建会话。
* 尽可能长时间保持会话活动状态（即，在一个会话中创建所有锚和查询）。

### <a name="enabling-bluetooth-beacons"></a>启用 Bluetooth 信标

假设你的应用程序已具有访问设备蓝牙状态的权限，则可以配置 Azure 空间锚点来使用它：

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.bluetoothEnabled = true;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.bluetoothEnabled = true
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setBluetoothEnabled(true);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->BluetoothEnabled(true);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors();
sensors.BluetoothEnabled(true);
```

---

信标通常是各种设备，其中包括 Uuid 和 MAC 地址-可以进行配置。 这种灵活性可能会对 Azure 空间锚定，这会使信标由其 Uuid 唯一标识。 未能确保这一唯一性最有可能转换为空间 wormholes。 为获得最佳结果，应执行以下操作：

* 向信标分配独特的 Uuid。
* 部署它们-通常采用常规模式（如网格）。
* 将唯一信标 Uuid 的列表传递给传感器指纹提供程序：

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.knownBeaconProximityUuids = uuids;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

let sensors = locationProvider?.sensors
sensors.knownBeaconProximityUuids = uuids
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setKnownBeaconProximityUuids(uuids);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->KnownBeaconProximityUuids(uuids);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

SensorCapabilities sensors = sensorProvider.Sensors();
sensors.KnownBeaconProximityUuids(uuids);
```

---

Azure 空间锚点将只跟踪列表中的蓝牙信标。 不过，被设计为具有允许列表的 Uuid 的恶意信标仍会对服务质量产生负面影响。 出于此原因，只应在特选空间中使用信标来控制它们的部署。

## <a name="querying-with-sensor-data"></a>查询传感器数据

使用关联的传感器数据创建锚点后，可以开始使用设备报告的传感器读数来检索它们。 你不再需要为该服务提供你要查找的已知锚，而只需让服务知道设备的位置（由其板载传感器报告）。 然后，空间锚定服务将确定靠近设备的一组锚，并尝试以视觉方式匹配它们。

若要让查询使用传感器数据，请先创建一个查找条件：

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0f;

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25;

ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let nearDeviceCriteria = ASANearDeviceCriteria()!

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25

let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.setDistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.setMaxResultCount(25);

AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria->DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria->MaxResultCount(25);

auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
NearDeviceCriteria nearDeviceCriteria = NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = AnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
```

---

`DistanceInMeters` 参数控制将浏览定位点图以检索内容的距离。 假设你已使用每个计量的固定密度的固定空间填充了一些空间。 此外，设备上的摄像机正在观察单个锚点，并且该服务已成功定位。 您很可能想要检索您在附近放置的所有定位点，而不是当前观察到的单个锚。 假设您所放置的定位点已连接到图形中，则该服务可以按照图中的边缘来检索所有附近的定位点。 完成的图形遍历量由 `DistanceInMeters`来控制;系统会将所有定位点连接到已找到的定位点，这比 `DistanceInMeters`更接近。

请记住，`MaxResultCount` 的大值可能会对性能产生负面影响。 尝试将其设置为对应用程序有意义的合理值。

最后，需要告诉会话使用基于传感器的查找：

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

---

## <a name="expected-results"></a>预期结果

消费者级 GPS 设备通常非常不精确。 按[Zandenbergen 和 Barbeau （2011）][6]进行的调查报告：移动电话的中值的中值为约7米，要忽略的值非常大！ 为了应对这些测量错误，服务将定位点视为 GPS 空间中的概率分布。 因此，定位点现在是最有可能的空间区域（即，具有超过95% 的置信度）包含其真正的未知 GPS 位置。

用 GPS 进行查询时，会应用相同的推理。 设备在其真正的未知 GPS 位置附近表示为另一个空间置信区。 发现附近的定位点可转换为仅查找置信区*近近*于设备置信区的定位点，如下图所示：

![选择包含 GPS 的定位点候选项](media/coarse-reloc-gps-separation-distance.png)

在创建锚点时以及在查询期间，GPS 信号的准确性会对返回的定位点集产生大的影响。 与此相反，基于 WiFi/信标的查询将考虑至少具有一个与查询共同的访问点/信标的所有锚。 从这种意义上讲，基于 WiFi/信标的查询的结果主要由访问点/信标的物理范围和环境障碍决定。

下表针对每种传感器类型估算了预期的搜索空间：

| 传感器      | 搜索空间半径（约为） | 详细信息 |
|-------------|:-------:|---------|
| GPS         | 20 m-30 m | 由其他因素的 GPS 不确定性决定。 报告的数字是使用-GPS 的移动电话的中间 GPS 准确性估算，这是7米。 |
| WiFi        | 50 m-100 m | 由无线访问点的范围确定。 取决于频率、发送器强度、物理障碍物、干扰等等。 |
| BLE 信标 |  70 m | 由信号范围决定。 取决于频率、传输强度、物理障碍物、干扰等等。 |

## <a name="per-platform-support"></a>每平台支持

下表总结了在每个受支持的平台上收集的传感器数据，以及任何特定于平台的警告：


|             | HoloLens | Android | iOS |
|-------------|----------|---------|-----|
| GPS         | N/A | 支持通过[LocationManager][3] API （GPS 和网络） | 通过[CLLocationManager][4] api 支持 |
| WiFi        | 支持，每3秒约扫描一次 | 。 从 API 级别28开始，每隔2分钟就会将 WiFi 扫描限制为4次调用。 可以从 Android 10 中禁用 "开发人员设置" 菜单中的限制。 有关详细信息，请参阅[Android 文档][5]。 | 不适用-无公共 API |
| BLE 信标 | 仅限[Eddystone][1]和[iBeacon][2] | 仅限[Eddystone][1]和[iBeacon][2] | 仅限[Eddystone][1]和[iBeacon][2] |

## <a name="next-steps"></a>后续步骤

在应用中使用粗 relocalization。

> [!div class="nextstepaction"]
> [Unity](../how-tos/set-up-coarse-reloc-unity.md)

> [!div class="nextstepaction"]
> [Objective-C](../how-tos/set-up-coarse-reloc-objc.md)

> [!div class="nextstepaction"]
> [Swift](../how-tos/set-up-coarse-reloc-swift.md)

> [!div class="nextstepaction"]
> [Java](../how-tos/set-up-coarse-reloc-java.md)

> [!div class="nextstepaction"]
> [C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)

> [!div class="nextstepaction"]
> [C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
