---
title: "Azure AD v2 iOS 入门 - 安装 | Microsoft Docs"
description: "iOS (Swift) 应用程序如何通过 Azure Active Directory v2 终结点调用需访问令牌的 API"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.translationtype: HT
ms.sourcegitcommit: b309108b4edaf5d1b198393aa44f55fc6aca231e
ms.openlocfilehash: 78c6ca83a2711ad81b7819a8723869980a1925a3
ms.contentlocale: zh-cn

---
## <a name="setting-up-your-ios-application"></a>设置 iOS 应用程序

本部分提供分步说明，介绍如何创建新项目，用于演示如何将 iOS 应用程序 (Swift) 与“登录 Microsoft”集成，使其能查询需要令牌的 Web API。

> 想要改为下载此示例的 XCode 项目？ [下载项目](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)并跳到[配置步骤](#create-an-application-express)，在执行前配置代码示例。


## <a name="install-carthage-to-download-and-build-msal"></a>安装 Carthage 以下载和生成 MSAL
Carthage 程序包管理器在 MSAL 预览期间使用 - 它与 XCode 集成，同时保留 Microsoft 对库做出更改的能力。

- 在[此处](https://github.com/Carthage/Carthage/releases "Carthage 下载 URL")下载和安装最新版本的 Carthage

## <a name="creating-your-application"></a>创建应用程序

1.  打开 Xcode，然后选择 `Create a new Xcode project`
2.  选择 `iOS` > `Single view Application`，然后单击“下一步”
3.  提供产品名称并单击“下一步”
4.  选择一个文件夹以创建应用，然后单击“创建”

## <a name="build-the-msal-framework"></a>生成 MSAL Framework

请按照以下说明使用 Carthage 拉取和生成最新版本的 MSAL：

1.  打开 Bash 终端，然后转到应用的根文件夹
2.  复制以下内容并粘贴到 Bash 终端以创建“Cartfile”文件：

```bash
echo "github \"AzureAD/microsoft-authentication-library-for-objc\" \"master\"" > Cartfile
```
<!-- Workaround for Docs conversion bug -->
<ol start="3">
<li>
复制并粘贴以下内容。 此命令将依赖项提取到 Carthage/Checkouts 文件夹，然后生成 MSAL 库：
</li>
</ol>

```bash
carthage update
```

> 上述过程用于下载和生成 MMicrosoft 身份验证库 (MSAL)。 MSAL 负责获取、缓存和刷新用于访问受 Azure Active Directory v2 保护的 API 的用户令牌。

## <a name="add-the-msal-framework-to-your-application"></a>将 MSAL framework 添加到应用程序
1.  在 Xcode 中打开 `General` 选项卡
2.  转到 `Linked Frameworks and Libraries` 部分，然后单击 `+`
3.  选择 `Add other…`
4.  选择：`Carthage` > `Build` > `iOS` > `MSAL.framework`，然后单击“打开”。 会看到 `MSAL.framework` 添加到了列表。
5.  转到 `Build Phases` 选项卡，然后单击 `+` 图标并选择 `New Run Script Phase`
6.  将以下内容添加到脚本区域：

```text
/usr/local/bin/carthage copy-frameworks
```

<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
单击 <code>+</code> 将以下内容添加到 <code>Input Files</code>：
</li>
</ol>

```text
$(SRCROOT)/Carthage/Build/iOS/MSAL.framework
```

## <a name="creating-your-applications-ui"></a>创建应用程序 UI
项目模板中应自动创建了 Main.storyboard 文件。 请按照以下说明创建应用 UI：

1.  按住 Control 键并单击 `Main.storyboard` 打开上下文菜单，然后单击：`Open As` > `Source Code`
2.  用以下代码替换 `<scenes>` 节点：

```xml
 <scenes>
    <scene sceneID="tne-QT-ifu">
        <objects>
            <viewController id="BYZ-38-t0r" customClass="ViewController" customModule="MSALiOS" customModuleProvider="target" sceneMemberID="viewController">
                <layoutGuides>
                    <viewControllerLayoutGuide type="top" id="y3c-jy-aDJ"/>
                    <viewControllerLayoutGuide type="bottom" id="wfy-db-euE"/>
                </layoutGuides>
                <view key="view" contentMode="scaleToFill" id="8bC-Xf-vdC">
                    <rect key="frame" x="0.0" y="0.0" width="375" height="667"/>
                    <autoresizingMask key="autoresizingMask" widthSizable="YES" heightSizable="YES"/>
                    <subviews>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Microsoft Authentication Library" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="ifd-fu-zjm">
                            <rect key="frame" x="64" y="28" width="246" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Logging" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="98g-dc-BPL">
                            <rect key="frame" x="16" y="277" width="62" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="2rX-Vv-T1i">
                            <rect key="frame" x="87" y="100" width="200" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Call Microsoft Graph API"/>
                            <connections>
                                <action selector="callGraphButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="Kx0-JL-Bv9"/>
                            </connections>
                        </button>
                        <textView clipsSubviews="YES" multipleTouchEnabled="YES" contentMode="scaleToFill" fixedFrame="YES" editable="NO" textAlignment="natural" selectable="NO" translatesAutoresizingMaskIntoConstraints="NO" id="qXW-2z-J7K">
                            <rect key="frame" x="16" y="324" width="343" height="291"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <color key="backgroundColor" white="1" alpha="1" colorSpace="calibratedWhite"/>
                            <accessibility key="accessibilityConfiguration">
                                <accessibilityTraits key="traits" updatesFrequently="YES"/>
                            </accessibility>
                            <fontDescription key="fontDescription" type="system" pointSize="14"/>
                            <textInputTraits key="textInputTraits" autocapitalizationType="sentences"/>
                        </textView>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="9u4-b8-vmX">
                            <rect key="frame" x="137" y="138" width="100" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Sign-Out"/>
                            <connections>
                                <action selector="signoutButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="kZT-P8-0Zy"/>
                            </connections>
                        </button>
                    </subviews>
                    <color key="backgroundColor" red="1" green="1" blue="1" alpha="1" colorSpace="custom" customColorSpace="sRGB"/>
                </view>
                <connections>
                    <outlet property="loggingText" destination="qXW-2z-J7K" id="uqO-Yw-AsK"/>
                    <outlet property="signoutButton" destination="9u4-b8-vmX" id="OCh-qk-ldv"/>
                </connections>
            </viewController>
            <placeholder placeholderIdentifier="IBFirstResponder" id="dkx-z0-nzr" sceneMemberID="firstResponder"/>
        </objects>
        <point key="canvasLocation" x="140" y="137.18140929535232"/>
    </scene>
</scenes>
```

