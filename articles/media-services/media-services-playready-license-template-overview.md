---
title: "媒体服务 PlayReady 许可证模板概述"
description: "本主题概述了用于配置 PlayReady 许可证的 PlayReady 许可证模板。"
author: juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: fddce5d0-1278-478f-ae05-9b985c748731
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 2b21765c53c9f9f37efb71924f12a5135170f788
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2018
---
# <a name="media-services-playready-license-template-overview"></a>媒体服务 PlayReady 许可证模板概述
Azure 媒体服务现在提供了用于交付 PlayReady 许可证的服务。 当播放器（例如 Silverlight）尝试播放受 PlayReady 保护的内容时，将向许可证交付服务发送请求以获取许可证。 如果许可证服务批准了该请求，则会颁发该许可证，该许可证将发送到客户端，并用来解密和播放指定的内容。

媒体服务还提供了可以用来配置 PlayReady 许可证的 API。 许可证包含了当用户尝试播放受保护的内容时要由 PlayReady 数字版权管理 (DRM) 运行时强制实施的权限和限制。
下面是可以指定的 PlayReady 许可证限制的一些示例：

* 该许可证开始生效的日期和时间。
* 许可证过期时的日期/时间值。 
* 要在客户端的永久性存储区保存的许可证。 永久性许可证通常用于允许脱机播放内容。
* 播放器必须具有的要播放内容的最低安全级别。 
* 音频\视频内容的输入控件的输出保护级别。 
* 有关详细信息，请参阅 [PlayReady 符合性规则](https://www.microsoft.com/playready/licensing/compliance/)文档中的“输出控件”部分 (3.5)。

> [!NOTE]
> 目前，只能配置 PlayReady 许可证的 PlayRight。 此权限是必需的。 PlayRight 赋予客户端播放内容的权限。 还可以使用 PlayRight 配置特定于播放的限制。 有关详细信息，请参阅 [PlayReadyPlayRight](media-services-playready-license-template-overview.md#PlayReadyPlayRight)。
> 
> 

若要使用媒体服务配置 PlayReady 许可证，必须配置媒体服务 PlayReady 许可证模板。 该模板在 XML 中定义。

下例演示了配置基本的流式处理许可证的最简单（也是最常见的）模板。 使用此许可证时，客户端可以播放受 PlayReady 保护的内容。

    <?xml version="1.0" encoding="utf-8"?>
    <PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" 
                                      xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
      <LicenseTemplates>
        <PlayReadyLicenseTemplate>
          <ContentKey i:type="ContentEncryptionKeyFromHeader" />
          <PlayRight />
        </PlayReadyLicenseTemplate>
      </LicenseTemplates>
    </PlayReadyLicenseResponseTemplate>

XML 遵循“PlayReady 许可证模板 XML 架构”部分中定义的 PlayReady 许可证模板 XML 架构。

媒体服务还可定义一组可用于序列化到 XML 和从 XML 反序列化的 .NET 类。 有关主要类的说明，请参阅用于配置许可证模板的[媒体服务 .NET 类](media-services-playready-license-template-overview.md#classes)。

有关使用 .NET 类来配置 PlayReady 许可证模板的端到端示例，请参阅[使用 PlayReady 动态加密和许可证交付服务](media-services-protect-with-playready-widevine.md)。

## <a id="classes"></a>用于配置许可证模板的媒体服务 .NET 类
下列类是用于配置媒体服务 PlayReady 许可证模板的主要 .NET 类。 这些类映射到 [PlayReady 许可证模板 XML 架构](media-services-playready-license-template-overview.md#schema)中定义的类型。

[MediaServicesLicenseTemplateSerializer](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.mediaserviceslicensetemplateserializer.aspx) 类用于序列化到媒体服务许可证模板 XML 和从该 XML 进行反序列化。

### <a name="playreadylicenseresponsetemplate"></a>PlayReadyLicenseResponseTemplate
[PlayReadyLicenseResponseTemplate](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadylicenseresponsetemplate.aspx)：此类表示发送回用户的响应的模板。 它包含一个在许可证服务器与应用程序之间使用的自定义数据字符串的字段（对于自定义应用逻辑可能比较有用）。 它还包含一个内含一个或多个许可证模板的列表。

作为模板层次结构中的“顶层”类，响应模板包含一个许可证模板列表。 许可证模板（直接或间接）包含构成要序列化的模板数据的所有其他类。

### <a name="playreadylicensetemplate"></a>PlayReadyLicenseTemplate
[PlayReadyLicenseTemplate](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadylicensetemplate.aspx)：此类表示用于创建要返回给用户的 PlayReady 许可证的许可证模板。 它包含关于许可证中的内容密钥的数据。 它还包括当使用内容密钥时，PlayReady DRM 运行时必须强制实施的任何权限或限制。

### <a id="PlayReadyPlayRight"></a>PlayReadyPlayRight
[PlayReadyPlayRight](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadyplayright.aspx)：此类表示 PlayReady 许可证的 PlayRight。 它授权用户播放受许可证中和 PlayRight 本身（对于特定于播放的策略）配置的任何限制所制约的内容。 针对 PlayRight 的策略大部分涉及输出限制，这些限制控制在播放内容时可以使用的输出类型。 它还包括在使用给定输出时必须实施的任何限制。 例如，如果启用了 DigitalVideoOnlyContentRestriction，则 DRM 运行时仅允许以数字输出形式播放视频。 （不允许以模拟视频输出形式来传送内容。）

> [!IMPORTANT]
> 这些类型的限制可能很有效，但也会影响使用者体验。 如果输出保护施加了太多限制，内容可能会无法在某些客户端上播放。 有关详细信息，请参阅 [PlayReady 符合性规则](https://www.microsoft.com/playready/licensing/compliance/)。
> 
> 

有关 Silverlight 支持的保护级别的示例，请参阅 [Silverlight 支持的输出保护](http://go.microsoft.com/fwlink/?LinkId=617318)。

## <a id="schema"></a>PlayReady 许可证模板 XML 架构
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:ser="http://schemas.microsoft.com/2003/10/Serialization/" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
      <xs:import namespace="http://schemas.microsoft.com/2003/10/Serialization/" />
      <xs:complexType name="AgcAndColorStripeRestriction">
        <xs:sequence>
          <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction" />
      <xs:simpleType name="ContentType">
        <xs:restriction base="xs:string">
          <xs:enumeration value="Unspecified" />
          <xs:enumeration value="UltravioletDownload" />
          <xs:enumeration value="UltravioletStreaming" />
        </xs:restriction>
      </xs:simpleType>
      <xs:element name="ContentType" nillable="true" type="tns:ContentType" />
      <xs:complexType name="ExplicitAnalogTelevisionRestriction">
        <xs:sequence>
          <xs:element minOccurs="0" name="BestEffort" type="xs:boolean" />
          <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ExplicitAnalogTelevisionRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction" />
      <xs:complexType name="PlayReadyContentKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="PlayReadyContentKey" nillable="true" type="tns:PlayReadyContentKey" />
      <xs:complexType name="ContentEncryptionKeyFromHeader">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:PlayReadyContentKey">
            <xs:sequence />
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="ContentEncryptionKeyFromHeader" nillable="true" type="tns:ContentEncryptionKeyFromHeader" />
      <xs:complexType name="ContentEncryptionKeyFromKeyIdentifier">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:PlayReadyContentKey">
            <xs:sequence>
              <xs:element minOccurs="0" name="KeyIdentifier" type="ser:guid" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="ContentEncryptionKeyFromKeyIdentifier" nillable="true" type="tns:ContentEncryptionKeyFromKeyIdentifier" />
      <xs:complexType name="PlayReadyLicenseResponseTemplate">
        <xs:sequence>
          <xs:element name="LicenseTemplates" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
          <xs:element minOccurs="0" name="ResponseCustomData" nillable="true" type="xs:string">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
        </xs:sequence>
      </xs:complexType>
      <xs:element name="PlayReadyLicenseResponseTemplate" nillable="true" type="tns:PlayReadyLicenseResponseTemplate" />
      <xs:complexType name="ArrayOfPlayReadyLicenseTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfPlayReadyLicenseTemplate" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
      <xs:complexType name="PlayReadyLicenseTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AllowTestDevices" type="xs:boolean" />
          <xs:element minOccurs="0" name="BeginDate" nillable="true" type="xs:dateTime">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element name="ContentKey" nillable="true" type="tns:PlayReadyContentKey" />
          <xs:element minOccurs="0" name="ContentType" type="tns:ContentType">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ExpirationDate" nillable="true" type="xs:dateTime">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="GracePeriod" nillable="true" type="ser:duration">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="LicenseType" type="tns:PlayReadyLicenseType" />
          <xs:element minOccurs="0" name="PlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
      <xs:simpleType name="PlayReadyLicenseType">
        <xs:restriction base="xs:string">
          <xs:enumeration value="Nonpersistent" />
          <xs:enumeration value="Persistent" />
        </xs:restriction>
      </xs:simpleType>
      <xs:element name="PlayReadyLicenseType" nillable="true" type="tns:PlayReadyLicenseType" />
      <xs:complexType name="PlayReadyPlayRight">
        <xs:sequence>
          <xs:element minOccurs="0" name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="AllowPassingVideoContentToUnknownOutput" type="tns:UnknownOutputPassingOption">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="AnalogVideoOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="CompressedDigitalAudioOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="CompressedDigitalVideoOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="DigitalVideoOnlyContentRestriction" type="xs:boolean">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ExplicitAnalogTelevisionOutputRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="FirstPlayExpiration" nillable="true" type="ser:duration">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ImageConstraintForAnalogComponentVideoRestriction" type="xs:boolean">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ImageConstraintForAnalogComputerMonitorRestriction" type="xs:boolean">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="UncompressedDigitalAudioOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="UncompressedDigitalVideoOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
        </xs:sequence>
      </xs:complexType>
      <xs:element name="PlayReadyPlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
      <xs:simpleType name="UnknownOutputPassingOption">
        <xs:restriction base="xs:string">
          <xs:enumeration value="NotAllowed" />
          <xs:enumeration value="Allowed" />
          <xs:enumeration value="AllowedWithVideoConstriction" />
        </xs:restriction>
      </xs:simpleType>
      <xs:element name="UnknownOutputPassingOption" nillable="true" type="tns:UnknownOutputPassingOption" />
      <xs:complexType name="ScmsRestriction">
        <xs:sequence>
          <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction" />
    </xs:schema>



## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

