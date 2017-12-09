
## <a name="setting-up-your-ios-application"></a>设置 iOS 应用程序

本部分提供有关如何创建新项目的分步说明来演示如何将 iOS 应用程序 (Swift) 集成*在使用 Microsoft 登录*以便可以进行查询，将需要一个令牌的 Web Api。

> 希望改为下载此示例的 XCode 项目？ [下载的项目](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)并跳到[配置步骤](#create-an-application-express)配置在执行之前的代码示例。


## <a name="install-carthage-to-download-and-build-msal"></a>安装 Carthage 以下载并构建 MSAL
MSAL 在预览期间使用 Carthage 程序包管理器 – 它与 XCode 集成同时保持适用 Microsoft 到库中进行更改的功能。

- 下载并安装最新版本的 Carthage[此处](https://github.com/Carthage/Carthage/releases "Carthage 下载 URL")

## <a name="creating-your-application"></a>创建你的应用程序

1.  打开 Xcode，然后选择`Create a new Xcode project`
2.  选择`iOS`  >  `Single view Application`单击*下一步*
3.  为产品名称，然后单击*下一步*
4.  选择一个文件夹来创建你的应用程序并单击*创建*

## <a name="build-the-msal-framework"></a>生成 MSAL Framework

请按照下面以拉取，然后生成使用 Carthage MSAL 库的最新版本的说明操作：

1.  打开 bash 终端并转到应用程序的根文件夹
2.  复制下面和粘贴在 bash 终端创建 Cartfile 文件中：

```bash
echo "github \"AzureAD/microsoft-authentication-library-for-objc\" \"master\"" > Cartfile
```
<!-- Workaround for Docs conversion bug -->
<ol start="3">
<li>
复制并粘贴下面。 此命令到 Carthage/签出的文件夹，提取依赖项，然后生成 MSAL 库：
</li>
</ol>

```bash
carthage update
```

> 上述过程用于下载并构建 Microsoft 身份验证库 (MSAL)。 MSAL 处理获取、 缓存和刷新用户令牌用于访问受 Azure Active Directory v2 的 Api。

## <a name="add-the-msal-framework-to-your-application"></a>将 MSAL 框架添加到你的应用程序
1.  在 Xcode 中，打开`General`选项卡
2.  转到`Linked Frameworks and Libraries`部分并单击`+`
3.  选择`Add other…`
4.  Select: `Carthage` > `Build` > `iOS` > `MSAL.framework` and click *Open*. 你应该会看到`MSAL.framework`添加到列表。
5.  转到`Build Phases`卡，然后单击`+`图标，选择`New Run Script Phase`
6.  添加以下内容到*脚本区域*:

```text
/usr/local/bin/carthage copy-frameworks
```

<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
添加到以下<code>Input Files</code>通过单击<code>+</code>:
</li>
</ol>

```text
$(SRCROOT)/Carthage/Build/iOS/MSAL.framework
```

## <a name="creating-your-applications-ui"></a>创建应用程序的 UI
Main.storyboard 文件自动应作为你的项目模板的一部分创建。 请按照下面的说明来创建应用程序 UI 操作：

1.  Control + 单击`Main.storyboard`以打开上下文菜单，然后单击：`Open As` > `Source Code`
2.  替换`<scenes>`节点替换为以下代码：

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
