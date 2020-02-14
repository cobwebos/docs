---
title: Azure Maps 术语表 | Microsoft Docs
description: 与 Azure Maps、基于位置的服务以及 GIS 相关的常用术语的术语表。
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: a6c1e14e332f6081111d2a58a30702093b99a876
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198235"
---
# <a name="glossary"></a>术语表

以下列表描述了与 Azure Maps 服务一起使用的常用词。

## <a name="a"></a>A

<a name="address-validation"></a>**地址验证**：验证是否存在地址的过程。

<a name="advanced-routing"></a>**高级路由**：使用公路路由数据执行高级操作的服务集合;例如，计算可访问的范围（等时线）、距离矩阵和批处理路由请求。

<a name="aerial-imagery"></a>**高空图像**：请参阅[卫星图像](#satellite-imagery)。 

<a name="along-a-route-search"></a>**沿着路由搜索**：用于在指定的 detour 时间内查找数据的空间查询，或与路由路径之间的距离。

<a name="altitude"></a>**海拔**：某个点在参考面上方的高度或垂直提升。 海拔高度测量基于给定的参考基准，例如平均海平面。 另请参阅海拔。

<a name="ambiguous"></a>不**明确**：当对象可以适当地分配给某个给定特性的两个或更多值时，数据分类中存在的不确定性状态。 例如，当地理编码 "CA" 时，将返回两个不明确的结果： "加拿大" 和 "加利福尼亚"。 "CA" 分别是国家/地区和省/市/自治区代码。 

<a name="annotation"></a>**批注**：在地图上显示的文本或图形，用于向用户提供信息。 注释可以标识或描述特定的地图实体，提供关于地图上的区域的一般信息，或者提供关于地图本身的信息。

<a name="antimeridian"></a>**Antimeridian**：也称为 180<sup>th</sup>经线。 这是-180 度和180度的经度达到的点。 这与地球上的质数经线相反。

<a name="application-programming-interface-api"></a>**应用程序编程接口（API）** ：允许开发人员创建应用程序的规范。

<a name="api-key"></a>**API 密钥**：请参阅[共享密钥身份验证](#shared-key-authentication)。

<a name="area-of-interest-aoi"></a>**感兴趣的区域（AOI）** ：用于定义地图或数据库生产的焦点区域的范围。

<a name="asset-tracking"></a>**资产跟踪**：跟踪资产位置的过程，如人员、车辆或其他某个对象。

<a name="asynchronous-request"></a>**异步请求**：打开连接并向返回异步请求标识符的服务器发出请求的 HTTP 请求，然后关闭连接。 服务器继续处理请求，用户可以使用标识符检查状态。 当请求完成处理后，用户可以下载响应。 此类请求用于长时间运行的进程。

<a name="autocomplete"></a>**自动完成**：应用程序中的一项功能可预测用户正在键入的单词的其余部分。 

<a name="autosuggest"></a>**自动建议**：应用程序中的一项功能可预测用户键入内容的逻辑可能性。

<a name="azure-location-based-services-lbs"></a>**Azure Location Based Services （磅）** ：在预览时 Azure Maps 的以前名称。

<a name="azure-active-directory"></a>**Azure Active Directory （Azure AD）** ： Azure AD 是 Microsoft 的基于云的标识和访问管理服务。 Azure Maps Azure AD 集成当前适用于所有 Azure Maps Api 的预览。 Azure AD 支持基于角色的访问控制（RBAC），以允许对 Azure Maps 资源进行精细访问。 若要详细了解 Azure Maps Azure AD 集成，请参阅在 Azure Maps 中[Azure Maps 和 Azure AD](azure-maps-authentication.md)和[管理身份验证](how-to-manage-authentication.md)。

<a name="azure-maps-key"></a>**Azure Maps 密钥**：请参阅[共享密钥身份验证](#shared-key-authentication)。

## <a name="b"></a>B

<a name="base-map"></a>**基本图**：地图应用程序的一部分，用于显示道路、特征点和政治边界等背景参考信息。

<a name="batch-request"></a>**批处理请求**：将多个请求合并为单个请求的过程。

<a name="bearing"></a>**轴承**：某个点相对于另一个点的水平方向。 这表示为相对于北的角度，以顺时针方向从0度到360度。 

<a name="boundary"></a>**边界**：分隔相邻政治实体（如国家/地区、地区和属性）的线条或多边形。 边界是可能或可能不遵循物理特征（例如河流、山脉或墙壁）的线。

<a name="bounds"></a>**边界**：请参阅[边界框](#bounding-box)。

<a name="bounding-box"></a>**边界框**：一组用于表示地图上的矩形区域的坐标。 

## <a name="c"></a>C

<a name="cadastre"></a>**Cadastre**：注册的陆地和属性的记录。 另请参阅 [Parcel](#parcel)。

<a name="camera"></a>**照相机**：在交互式地图控件的上下文中，照相机定义了视图的地图字段。 照相机的视区是根据几个地图参数确定的：中心、缩放级别、间距和对齐。 

<a name="centroid"></a>**质心**：功能的几何中心。 线的质心为其中点，而多边形的质心则为其中心区域。

<a name="choropleth-map"></a>**等值线图 map**：一个主题地图，其中的区域与统计变量的度量值成正比。 此统计变量显示在地图上。 例如，根据美国每个州相对于其他所有州的人口，将美国每个州的边界着色。

<a name="concave-hull"></a>**凹陷凸**形：表示可能包含指定数据集中的所有形状的凹几何的形状。 生成的形状类似于用保鲜膜包裹数据然后加热它，从而导致点之间的大跨度向其他数据点塌陷。

<a name="consumption-model"></a>**消耗模型**：定义车辆消耗燃料或电的速率的信息。 另请参阅[消耗模型文档](consumption-model.md)。

<a name="control"></a>**控件**：自包含或可重复使用的组件，其中包含一个图形用户界面，用于定义一组针对该接口的行为。 例如，地图控件通常是加载交互式地图的用户界面的一部分。

<a name="convex-hull"></a>**凸**形凸形：凸形凸形是表示将所有形状包围在指定数据集中的最小凸几何的形状。 生成的形状类似于在数据集周围缠绕弹性带。

<a name="coordinate"></a>**坐标**：由用于表示地图上某个位置的经度和纬度值组成。

<a name="coordinate-system"></a>**坐标系统**：用于定义两个或三个维度中点的位置的参考框架。

<a name="country-code"></a>**国家**/地区代码：基于 ISO 标准的国家/地区的唯一标识符。 ISO2 是一个国家/地区的双字符代码（例如 US），ISO3 则表示三字符代码（例如 USA）。

<a name="country-subdivision"></a>**国家**/地区划分：国家/地区的第一层细分，通常称为州或省。

<a name="country-secondary-subdivision"></a>**国家/地区辅助细分**：一个国家/地区的二级细分，通常称为县。

<a name="country-tertiary-subdivision"></a>**国家三层细分**：一个国家/地区的第三层细分，通常是一个命名区域，如 ward。

<a name="cross-street"></a>**交叉街道**：与两个或更多个街道相交的点。

<a name="cylindrical-projection"></a>**圆柱形投影**：一种投影，用于转换从 spheroid 或球到切线或正割圆柱的点。 然后圆柱从顶部切至底部并平展为一个平面。

## <a name="d"></a>D

<a name="datum"></a>**基准**：度量系统的参考规格、图面上坐标位置的系统（水平基准）或图面上方或下方的高度（垂直基准）。

<a name="dbf-file"></a>**DBF 文件**：一种与形状文件（SHP）结合使用的数据库文件格式。

<a name="degree-minutes-seconds-dms"></a>**度分秒（DMS）** ：描述纬度和经度的度量单位。 1 度是一个圆的 1/360<sup></sup>。 1 度进一步分为 60 分钟，1 分钟分为 60 秒。

<a name="delaunay-triangulation"></a>**Delaunay 三角**化：一种方法，用于从点数据集中创建连续的、不重叠的三角形。 每个三角形的外接圆都不包含内部数据集中的点。

<a name="demographics"></a>**人口统计**信息：人机人口的统计特征（如年龄、出生率和收入）。

<a name="destination"></a>**目标**：某个终结点或某人前往的位置。

<a name="digital-elevation-model-dem"></a>**数字提升模型（DEM）** ：与图面相关的仰角值的数据集，使用通用基准按固定间隔在某个区域上捕获。 DEM 通常用于表示地形缓解。

<a name="dijkstra's-algorithm"></a>**Dijkstra 的算法**：检查网络连接以查找两个点之间的最短路径的算法。

<a name="distance-matrix"></a>**距离矩阵**：一个矩阵，其中包含一组源和目标之间的行程时间和距离信息。 

## <a name="e"></a>E

<a name="elevation"></a>**提升**：某个点或其上方或下方的某个对象的垂直距离。 通常，引用图面为海平面级。 高程一般是指地面的垂直高度。

<a name="envelope"></a>**信封**：请参阅[边界框](#bounding-box)。

<a name="extended-postal-code"></a>**扩展邮政编码**：可以包含其他信息的邮政编码。 例如，在美国，邮政编码包含五位数字。 但扩展的邮政编码（称为 zip + 4）包含四个其他数字。 这些附加数字用于标识五位数交付区内的地理段，如 city 块、一组单元或邮局箱。 了解地理分段有助于有效地进行邮件排序和传递。

<a name="extent"></a>**区**：请参阅[边界框](#bounding-box)。

## <a name="f"></a>F

<a name="federated-authentication"></a>**联合身份验证**：一种身份验证方法，允许跨多个 web 应用和移动应用使用单一登录/身份验证机制。 

<a name="feature"></a>**功能**：将几何图形与其他元数据信息组合在一起的对象。 

<a name="feature-collection"></a>**功能集合**：功能对象的集合。

<a name="find-along-route"></a>**沿路由进行查找**：一个空间查询，用于查找指定 detour 时间内或与某个路由路径的距离内的数据。

<a name="find-nearby"></a>**查找附近**的：用于从某个点搜索固定直线距离（如鱼尾纹飞出）的空间查询。

<a name="fleet-management"></a>**汽油管理**：商业汽车的管理，例如汽车、卡车、发货和飞机。 车队管理可以包括一系列功能，例如车辆融资、维护、远程信息处理（跟踪和诊断）以及驾驶员、速度、燃油以及运行状况和安全管理。 汽油管理是依赖其业务中的业务的公司所使用的过程。 公司希望最大程度地降低风险，降低其整体运输和员工成本，同时确保与政府法规的符合性。

<a name="free-flow-speed"></a>**自由流速度**：理想情况下预期的免费流速度。 通常是速度限制。

<a name="free-form-address"></a>**自由格式地址**：以单行文本形式表示的完整地址。

<a name="fuzzy-search"></a>**模糊搜索**：采用一串自由格式的搜索，该文本可能是一个地址或一个感兴趣的点。 

## <a name="g"></a>G

<a name="geocode"></a>**地理编码**：已转换为坐标的地址或位置，可用于在地图上显示该位置。 

<a name="geocoding"></a>**地理编码**：也称为 forward 地理编码，是将位置数据地址转换为坐标的过程。 

<a name="geodesic-path"></a>**测地线路径**：弯曲表面上两点之间的最短路径。 由于墨卡托投影，此路径将在 Azure Maps 上显示为曲线。

<a name="geofence"></a>**地域隔离区内**：一种定义的地理区域，可用于在设备进入或存在区域时触发事件。

<a name="geojson"></a>**GeoJSON**：是一种基于 JSON 的常见文件格式，用于存储点、线条和多边形等地理矢量数据。 **注意**：Azure Maps 使用 GeoJSON 的扩展版本，如[此处所述](extend-geojson.md)。

<a name="geometry"></a>**Geometry**：表示空间对象，如点、线条或多边形。

<a name="geometrycollection"></a>**GeometryCollection**： geometry 对象的集合。

<a name="geopol"></a>**GeoPol**：指 geopolitically 敏感数据，如有争议的边框和位置名称。

<a name="georeference"></a>**Georeference**：将地理数据或图像对齐到已知坐标系统的过程。 此过程可能包括移位、旋转、缩放或倾斜数据。

<a name="georss"></a>**GeoRSS**：用于向 RSS 源添加空间数据的 XML 扩展。

<a name="gis"></a>**GIS**： "地理信息系统" 的缩写。 用于描述测绘行业的常用术语。

<a name="gml"></a>**GML**：也称为地理标记语言。 用于存储空间数据的 XML 文件扩展名。

<a name="gps"></a>**GPS**：也称为全球定位系统，是一种用于在地球上确定设备位置的卫星系统。 轨道卫星发射信号，允许地球上任何地方的 GPS 接收器通过三边测量计算自己的位置。

<a name="gpx"></a> **.Gpx)** ：也称为 GPS 交换格式，是一种通常从 GPS 设备创建的 XML 文件格式。  

<a name="great-circle-distance"></a>**大圆距离**：球表面上两个点之间的最短距离。

<a name="greenwich-mean-time-gmt"></a>**格林尼治标准时间（GMT）** ：在经线中通过皇家天文台在英国的时间运行的时间。

<a name="guid"></a>**GUID**：全局唯一标识符。 用于唯一标识接口、类、类型库、组件类别或记录的字符串。

## <a name="h"></a>H

<a name="haversine-formula"></a>**半正矢公式**：用于计算球体上两点之间的大圆距离的常见公式。

<a name="hd-maps"></a>**高清地图**：也称为高清晰的地图，由高保真性公路网络信息（例如，公路驾驶所需的通道标记、告示和方向灯）组成。

<a name="heading"></a>**标题**：方向或方向的方向。 另请参阅[方位](#heading)。

<a name="heatmap"></a>**热度地图**：一种数据可视化，其中颜色范围表示特定区域中点的密度。 另请参阅“主题地图”。

<a name="hybrid-imagery"></a>**混合图像**：在其顶部有公路数据和标签的卫星或高空影像。

## <a name="i"></a>I

<a name="iana"></a>**IANA**： Internet 编号分配机构的首字母缩略词。 监督全球 IP 地址分配的非营利组织。

<a name="isochrone"></a>**Isochrone**：一个 Isochrone 定义一个区域，在该区域中，某人可以在指定时间内从给定位置的任意方向传输。 另请参阅[可访问范围](#reachable-range)。

<a name="isodistance"></a>**Isodistance**：给定位置，isochrone 定义一个区域，在此区域中，某人可以在指定距离内的任何方向传输。 另请参阅[可访问范围](#reachable-range)。

## <a name="k"></a>K

<a name="kml"></a>**KML**：也称为 Keyhole 标记语言，是一种用于存储地理矢量数据（如点、线条和多边形）的常用 XML 文件格式。 

## <a name="l"></a>L

<a name="landsat"></a>**Landsat**： Multispectral，由 NASA 开发的轨道卫星，用于收集土地的图像。 此图像用于许多行业，如农业、林和绘图。

<a name="latitude"></a>**纬度**：从赤道在北或南部方向上以度为单位的角距离。

<a name="level-of-detail"></a>**详细信息级别**：请参阅缩放级别。

<a name="lidar"></a>**Lidar**：光源检测和范围的缩写。 一种遥感技术，使用激光测量到反射表面的距离。

<a name="linear-interpolation"></a>**线性内插**：使用已知值之间的线性距离估算未知值。

<a name="linestring"></a>**LineString**：用于表示线条的几何。 也称为折线。 

<a name="localization"></a>**本地化**：支持不同的语言和区域性。

<a name="logistics"></a>**物流**：以协调方式移动人员、车辆、用品或资产的过程。

<a name="longitude"></a>**经度**：从东或西方向以度为单位的经线角度距离。

## <a name="m"></a>M

<a name="map-tile"></a>**地图磁贴**：表示地图画布分区的矩形图像。 有关详细信息，请参阅[缩放级别和磁贴网格文档](zoom-levels-and-tile-grid.md)。

<a name="marker"></a>**标记**：也称为 pin 或图钉，是表示地图上的点位置的图标。

<a name="mercator-projection"></a>**Mercator 投影**：一种用于海里的标准地图投影，这是因为它能够将恒定的直线（称为 rhumb 行）表示为与经线一起保留的直段。 与地球表面的真实布局相比，所有平面地图投影都会扭曲地图的形状或大小。 墨卡托投影会放大远离赤道的区域，因此接近两极时，较小的区域在地图上显得更大。 

<a name="multilinestring"></a>**MultiLineString**：表示 LineString 对象集合的几何图形。 

<a name="multipoint"></a>**MultiPoint**：表示点对象集合的几何图形。

<a name="multipolygon"></a>**MultiPolygon**：表示多边形对象的集合的几何图形。 例如，若要显示夏威夷的边界，则每个岛都带有多边形。 因此，夏威夷的边界会成为一个 MultiPolygon。

<a name="municipality"></a>**市政府**：市县。 

<a name="municipality-subdivision"></a>**市政府细分**：市政府的细分，如 "市区" 之类的邻近区域或本地区域名称。

## <a name="n"></a>N

<a name="navigation-bar"></a>**导航栏**：地图上用于调整缩放级别、间距、旋转和切换基本地图层的控件集。

<a name="nearby-search"></a>**邻近搜索**：从某个点搜索固定直线距离（与鱼尾纹飞出）的空间查询。

<a name="neutral-ground-truth"></a>**非特定**形式：以其所代表区域的官方语言（如果有）呈现标签的地图。

## <a name="o"></a>O

<a name="origin"></a>**源**：用户所在的开始点或位置。

## <a name="p"></a>P

<a name="panning"></a>**平移**：在保持恒定缩放级别的同时，以任意方向移动地图的过程。

<a name="parcel"></a>**包裹**：土地或属性边界的绘图。

<a name="pitch"></a>**螺距**：地图相对于垂直方向的倾斜量，其中0从地图向下查找。

<a name="point"></a>**Point**：表示地图上单个位置的几何。 

<a name="points-of-interest-poi"></a>**兴趣点（POI）** ：业务、路标或常见位置。

<a name="polygon"></a>**多边形**：表示地图上某个区域的实体几何。 

<a name="polyline"></a>**折线**：用于表示线条的几何。 也称为 LineString。 

<a name="position"></a>**位置**：点的经度、纬度和海拔高度（x，y，z 坐标）。

<a name="post-code"></a>**Post 代码**：请参阅[邮政编码](#postal-code)。

<a name="postal-code"></a>**邮政编码**：以特定格式指定的一系列字母或数字。 国家/地区的邮政服务使用该邮政编码来将地理区域划分为区域，以便简化邮件的传送。

<a name="primary-key"></a>**主键**：为 Azure Maps 共享密钥身份验证提供的两个订阅密钥中的第一个。 请参阅[共享密钥身份验证](#shared-key-authentication)。

<a name="prime-meridian"></a>**质数经线**：表示0度经度的一条经度。 通常情况下，在 westerly 方向下旅行时，经度值会减少到180度，在 easterly 方向上前进到-180 度。 

<a name="prj"></a>**扩展名为 .prj**：一个文本文件，通常随附一个形状文件文件，该文件包含有关数据集所在的投影坐标系的信息。

<a name="projection"></a>**投影**：基于地图投影的投影坐标系统，如横向 Mercator、Albers 等面积和 Robinson。 这些投影可将地球球面的地图投影到二维笛卡尔坐标平面。 投影坐标系统有时称为地图投影。

## <a name="q"></a>Q

<a name="quadkey"></a>**Quadkey**： quadtree 平铺系统内磁贴的地址索引。 有关详细信息，请参阅[缩放级别和磁贴网格](zoom-levels-and-tile-grid.md)文档。

<a name="quadtree"></a>**Quadtree**：一种数据结构，其中每个节点恰好具有四个子节点。 Azure Maps 中使用的平铺系统使用 quadtree 结构，以便用户放大一个级别，每个地图磁贴分为四 subtiles。  有关详细信息，请参阅[缩放级别和磁贴网格](zoom-levels-and-tile-grid.md)文档。

<a name="queries-per-second-qps"></a>**每秒查询数（QPS）** ：在一秒内可以对服务或平台发出的查询或请求数。 

## <a name="r"></a>R

<a name="radial-search"></a>**径向搜索**：从某个点搜索固定直线距离（如鱼尾纹飞出）的空间查询。 

<a name="raster-data"></a>**光栅数据**：组织为行和列（或网格）的单元格矩阵，其中每个单元格都包含一个表示信息的值（例如温度）。 光栅数据包括数字航空照片、卫星图像、数字图片和扫描地图。

<a name="raster-layer"></a>**光栅层**：包含光栅图像的图块层。

<a name="reachable-range"></a>可**访问范围**：可访问的范围定义了这样一种区域：在该区域中，某人可以在指定的时间内、从某个位置的任何方向传输到旅行模式。 另请参阅[等时线](#isochrone)和[等距离](#isodistance)。

<a name="remote-sensing"></a>**远程感应**：从远处收集和解释传感器数据的过程。

<a name="rest-service"></a>**Rest 服务**：首字母缩写词代表具象状态传输。 REST 服务是一种基于 URL 的 Web 服务，它依赖于基本的 Web 技术进行通信，最常见的方法是 HTTP GET 和 POST 请求。 与传统的基于 SOAP 的服务相比，这些类型的服务更快更小。

<a name="reverse-geocode"></a>**反向地理编码**：采用坐标并确定在地图上表示的地址的过程。

<a name="reproject"></a>**Reproject**：请参阅[转换](#transformation)。

<a name="rest-service"></a>**REST 服务**：具象状态传输的缩写。 在分散的分布式环境中用于在对等机之间交换信息的体系结构。 REST 允许不同计算机上的程序独立于操作系统或平台进行通信。 服务可以将超文本传输协议（HTTP）请求发送到统一资源定位器（URL），并获取数据。

<a name="route"></a>**Route**：两个或多个位置之间的路径，可能还包括其他信息，例如，沿路由的 waypoints 说明。

<a name="requests-per-second-rps"></a>**每秒请求数（RPS）** ：请参阅[每秒查询数（QPS）](#queries-per-second-qps)。 

<a name="rss"></a>**RSS**：真正简单的整合、资源描述框架（RDF）站点摘要或丰富的站点摘要（具体取决于源）的缩写。 一种简单的结构化 XML 格式，用于在不同网站之间共享内容。 RSS 文档包括关键元数据元素，例如作者、日期、标题、简要描述和超文本链接。 此信息有助于用户（或 RSS 发布者服务）决定哪些材料值得进一步调查。

## <a name="s"></a>S

<a name="satellite-imagery"></a>**卫星图像**：通过平面和附属向下直接捕获的图像。

<a name="secondary-key"></a>**辅助密钥**：为 Azure Maps 共享密钥身份验证提供的两个订阅密钥中的第二个。 请参阅[共享密钥身份验证](#shared-key-authentication)。

<a name="shapefile-shp"></a>**形状文件（SHP）** ：也称为 "ESRI 形状文件"，它是用于存储地理功能的位置、形状和属性的矢量数据存储格式。 形状文件存储在一组相关文件中。

<a name="shared-key-authentication"></a>**共享密钥身份验证**：共享密钥身份验证依赖于将 Azure Maps 帐户生成的密钥与每个请求传递到 Azure Maps。 这些密钥通常称为订阅密钥。 建议定期重新生成密钥，以确保安全。 提供了两个密钥，以便您可以在重新生成其他密钥时使用一个密钥维护连接。 重新生成密钥时，必须将访问此帐户的所有应用程序更新为使用新密钥。 若要详细了解 Azure Maps 身份验证，请参阅[Azure Maps 和 Azure AD](azure-maps-authentication.md) ，并[在 Azure Maps 中管理身份验证](how-to-manage-authentication.md)。

<a name="software-development-kit-sdk"></a>**软件开发工具包（SDK）** ：文档、示例代码和示例应用的集合，可帮助开发人员使用 API 生成应用。

<a name="spherical-mercator-projection"></a>**球形 Mercator 投影**：请参见[Web Mercator](#web-mercator)。 

<a name="spatial-query"></a>**空间查询**：对执行空间操作的服务发出的请求。 如径向搜索，或沿路由搜索。

<a name="spatial-reference"></a>**空间引用**：用于精确定位地理实体的基于坐标的本地、区域或全局系统。 它定义用于将地图坐标与现实世界中的位置相关联的坐标系。 空间引用确保可以集成来自不同层的空间数据或源，以便准确查看或分析。 Azure Maps 使用 [EPSG:3857](https://epsg.io/3857) 坐标参考系统和 WGS 84，以输入几何数据。

<a name="sql-spatial"></a>**SQL 空间**：指 SQL Azure 和 SQL Server 2008 及更高版本中内置的空间功能。 此空间功能也可作为 .NET 库使用，可以独立于 SQL Server 使用。 有关详细信息，请参阅[空间数据 (SQL Server) 文档](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)。

<a name="subscription-key"></a>**订阅密钥**：请参阅[共享密钥身份验证](#shared-key-authentication)。

<a name="synchronous-request"></a>**同步请求**： HTTP 请求打开连接并等待响应。 浏览器限制可从页面发出的并发 HTTP 请求的数量。 如果同时执行多个长时间运行的同步请求，则可以达到此限制。 请求将延迟，直到其中一个请求完成。

## <a name="t"></a>T

<a name="telematics"></a>**远程信息处理**：通过电信设备发送、接收和存储信息，以及控制对远程对象的控制。 

<a name="temporal-data"></a>**时态数据**：专门引用时间或日期的数据。 临时数据可能指离散事件，例如雷击、移动物体（例如火车）或重复观察结果（例如流量传感器计数）。

<a name="terrain"></a>**地形**：具有特定特征的陆地区域，如 sandy 地形或 mountainous 地形。

<a name="thematic-maps"></a>**主题 maps**：主题映射是一种简单的地图，用于反映有关地理区域的主题。 这种类型的映射的一个常见方案是基于某些数据指标为管理区域（如国家/地区）着色。

<a name="tile-layer"></a>图**块层**：通过将地图磁贴（矩形部分）装配到连续层中而显示的层。 图块分为两类：光栅图像图块和矢量图块。 通常会提前呈现光栅图块层，并将其存储为服务器上的图像。 光栅图块层可能会使用较大的存储空间。 矢量图块层在客户端应用程序中以接近实时的速度呈现。 因此，矢量图块层的服务器端存储要求更小。

<a name="time-zone"></a>**时区：全球**范围内遵循法律、商业和社交目的统一标准时间的区域。 时区往往遵循国家/地区及其细分的边界。

<a name="transaction"></a>**Transaction**： Azure Maps 使用事务授权模型，其中;

- 为每 15 个请求的地图或流量图块创建一个事务。
- 为 Azure Maps 中的其中一项服务的每个 API 调用创建一个事务。 搜索和路由是 Azure Maps 服务的示例。

<a name="transformation"></a>**转换**：在不同地理坐标系统之间转换数据的过程。 例如，你的一些数据可能在英国捕获且基于 OSGB 1936 地理坐标系统。 Azure Maps 使用 WGS84 的 [EPSG:3857](https://epsg.io/3857) 坐标参考系统变体。 因此，为了正确显示数据，需要将其坐标从一个系统转换为另一个系统。

<a name="traveling-salesmen-problem-tsp"></a>**旅行 Salesmen 问题（TSP）** ：一个 Hamiltonian 线路问题，销售人员必须找到最有效的一系列停止方法，然后返回到起始位置。  

<a name="trilateration"></a>**Trilateration**：通过测量所有三个点之间的距离，确定地球表面上某个点位置的过程。

<a name="turn-by-turn-navigation"></a>**轮流导航**：在用户使用下一个遍历时为路由的每个步骤提供路由说明的应用程序。

## <a name="v"></a>V

<a name="vector-data"></a>**矢量数据**：基于坐标的数据，表示为点、线条或多边形。

<a name="vector-tile"></a>**矢量磁贴**：用于存储地理空间矢量数据的开放数据规范，使用同一磁贴系统作为地图控件。 另请参阅[图块层](#tile-layer)。

<a name="vehicle-routing-problem-vrp"></a>**车辆路由问题（VRP）** ：一类问题，其中在考虑作为约束集时，将为一组车辆计算一组有序路由。 这些限制可能包括交付时间窗口、多个路由容量和出差持续时间约束。

<a name="voronoi-diagram"></a>**Voronoi 图**：环绕一组几何对象（通常为点特征）的区域或单元的空间分区。 这些单元格或多边形必须满足 Delaunay 三角形的标准。 与该集中的其他对象相比，区域内的所有位置都更靠近其围绕的对象。 Voronoi 图通常用于描绘地理特征周围的影响区域。 

## <a name="w"></a>W

<a name="waypoint"></a>**Waypoint**： Waypoint 是由经度和纬度定义的、用于导航的地理位置。 通常用于表示某人导航路线的点。

<a name="waypoint-optimization"></a>**Waypoint 优化**：重新排序一组 waypoints 的过程，以最大程度地减少传递所有提供的 waypoints 所需的行程时间或距离。 根据优化的复杂程度，此优化通常称为[旅行 Salesmen 问题](#traveling-salesmen-problem-tsp)或[车辆路线问题](#vehicle-routing-problem-vrp)。

<a name="web-map-service-wms"></a>**Web 映射服务（wms）** ： WMS 是开放地理联盟（OGC）标准，用于定义基于图像的地图服务。 WMS 服务根据需要为地图内的特定区域提供地图图像。 图像包括预呈现的符号系统，若由服务定义，则可以其中一种命名样式呈现。

<a name="web-mercator"></a>**Web Mercator**：也称为球状 Mercator 投影。 这是 Mercator 投影的轻微变体，主要用于基于 Web 的映射程序。 它使用与用于小比例地图的标准墨卡托投影相同的公式。 但是，Web Mercator 在所有规模上都使用球状公式，但大规模 Mercator 映射通常使用椭圆体形式的投影。 这种差异是在全球范围内让的，但它会导致在同一刻度上从真正的椭圆体 Mercator 地图到略有偏差的本地区域的映射。

<a name="wgs84"></a>**WGS84**：一组用于将空间坐标与地图图图面上的位置相关联的常量集。 WGS84 基准是大多数在线地图提供程序和 GPS 设备使用的标准基准。 Azure Maps 使用 WGS84 的 [EPSG:3857](https://epsg.io/3857) 坐标参考系统变体。

## <a name="z"></a>Z

<a name="z-coordinate"></a>**Z 坐标**：请参阅[海拔高度](#altitude)。 

<a name="zip-code"></a>**邮政编码**：请参阅[邮政编码](#postal-code)。

<a name="Zoom level"></a>**缩放级别**：指定详细信息的级别以及地图的可见程度。 当放大到级别0时，全世界地图通常将可见。 但地图将显示有限的详细信息，例如国家/地区名称、边框和海运名称。 当放大到接近等级 17 时，地图将显示具有详细道路信息的几个城市街区的区域。 在 azure 地图中，最高缩放级别为22。 有关详细信息，请参阅[缩放级别和磁贴网格](zoom-levels-and-tile-grid.md)文档。

