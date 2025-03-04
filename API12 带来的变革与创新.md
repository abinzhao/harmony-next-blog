> 随着前几天 HarmonyOS NEXT 版本的公测，很多小伙伴已经用上了纯血鸿蒙，丝滑的使用体验给大家带来更强大的功能与卓越体验，老实说，掌门人也是微信的系统级推送，调用原生相机等等，以及最让我惊喜的是应用可以调用系统级动画，让整个软件更加丝滑。
>
> HarmonyOS NEXT Beta1 在 HarmonyOS NEXT Developer Beta6 的基础上实现了重大突破，不仅新增了众多 C API 能力，还对系统的稳定性和兼容性进行了深度优化。
>
> 今天为大家简单的科普一下关于这次 next 版本的拓展以及 API12 的改进。

## 一、版本演进与新特性

### 版本状态演进：

从此版本起，版本状态由**开发者 Beta** 演进到 Beta 状态。开发者 Beta 阶段仅供开发者申请系统版本并获取开发套件用于应用开发；Beta 阶段允许更多消费者申请系统版本，同时也表示系统更加稳定，开发者也可以得到更好的开发体验。

### 二、OS 平台能力增强：

**2.1 Ability Kit**：新增支持 want 属性中 parameter 参数的更多描述信息，包括 callerAbilityName（拉起方的 AbilityName）、callerNativeName（Native 调用时拉起方的进程名）、callerAppId（拉起应用的 AppId 信息）、callerAppIdentifier（拉起应用的 AppIdentifier 信息）。还新增 API 用以支持拉起目标方 Ability 并在目标方返回结果的同时返回拉起动作的发起方。

**2.2 Account Kit**：支持应用在使用未成年模式之前，判断当前设备环境是否支持未成年人模式，并提供华为账号一键登录按钮显示的文本内容，包括 “华为账号一键注册” 和 “华为账号一键登录”。

**2.3 ArkGraphics 2D**：新增矩形网格对象 Lattice，用于将图片按矩形网格进行划分，还新增支持描述文本行中连续文本块的度量信息（RunMetrics）和文本布局中单行文字的度量信息（LineMetrics），同时对同一个帧同步信号支持多个 callback 回调实例，新增 Window Buffer 的 C-API 定义 NativeWindowBuffer。

**2.4 ArkUI**：

支持获取状态管理框架代理前的原始对象，通过 C-API 获取组件标识 ID（NODE_UNIQUE_ID），Refresh 组件的 RefreshOption 对象支持 ComponentContent 类型的对象 refreshingContent，用于自定义刷新区域显示的内容。

支持通过 C API 自定义段落组件的测量信息（ArkUI_CustomSpanMeasureInfo）、度量指标（ArkUI_CustomSpanMetrics）、绘制信息（ArkUI_CustomSpanDrawInfo）。

Navigation 组件支持将页面栈内指定 navDestinationId 的 NavDestination 页面删除。

RichEditor 支持配置获焦时是否拉起软键盘。新增 API 支持测量字符宽度。

点击事件和手势支持设置触控阈值 distanceThreshold。

Text 组件可设置文本是否将行间距平分至行的顶部与底部。获取已加载的组件的截图新增同步接口。

新增空闲时间的生命周期回调用以支持应用闲时处理耗时任务。

支持通过路由模式创建画中画。

支持按照 options 中的配置参数创建 XComponent 类型的 FrameNode 节点。

List 组件支持设置在显示区域以外删除数据后保持显示区域内容不变的能力。

提供查询窗口状态和查询窗口是否获焦的 API。

新增 C-API 用于将普通不可观察数据变为可观察数据。

Column 组件支持设置其子组件在主轴（即竖直方向）上反转排列。

新增支持通过 C-API 管理屏幕的能力。

CustomDialog 组件和弹窗模块（ohos.promptAction）支持通过 keyboardAvoidMode 属性设置弹窗是否在拉起软键盘时进行自动避让。

TextArea 组件可设置点击回车键后软键盘不收起。

**2.5 ArkWeb**：

支持通过 W3C 标准协议接口对接运动和方向相关的传感器。

新增 ScrollType 类型定义，用于增强嵌套滚动能力。

新增 C-API 的 Post Message 能力。

新增 API 用于支持自定义系统菜单。

新增 API 用于支持同层渲染可见性发生变化时的回调。

新增 API 用于在滚动事件以外设置将页面滚动指定的偏移量并返回执行结果。

**Basic Services Kit**：新增支持通过 C-API 获取和使用时间时区的能力。新增支持通过 C-API 订阅 / 退订公共事件的能力。

**Call Kit**：上报来电时，支持指定通话类型是否为会议、视频会议是否支持语音接听。通话状态支持正在接听、正在断开。上报通话状态改变时，支持指定通话类型。

**Car Kit**：新增管理应用与系统的连接状态，包括对连接状态的监听（指南）、取消监听（指南）和查询（指南）。新增管理应用与系统的事件通知，包括对事件通知的监听（指南）、取消监听（指南）和查询（指南）。在设置导航数据场景下，支持自定义模式传递导航元数据。

**Camera Kit**：新增一批 C-API，完善相机 C-API 能力，例如获取图片对象的能力（photo_native.h）、拍照开始信息的定义（Camera_CaptureStartInfo）、曝光结束信息的定义（Camera_FrameShutterEndInfo）、平滑变焦参数信息的定义（Camera_SmoothZoomInfo）、手电筒状态信息的定义（Camera_TorchStatusInfo）等。

**Device Security Kit**：新增安全审计 API，支持应用获取安全审计数据，审计数据包括窗口截屏事件、USB 插拔事件、剪切板复制粘贴事件等。

**Graphics Accelerate Kit**：新增 API 支持获取 ABR 自适应缩放后的纹理索引。新增 API 支持获取下一帧的 ABR Buffer 分辨率因子。

**HiAI Foundation Kit**：新增单算子特性，用于三方框架将部分算子通过单算子对接的方式迁移至 NPU，与整网 CPU 计算相比，性能更优。

**Image Kit**：支持通过 C-API 设置图像跨距，即图像每行占用的真实内存大小。Pixelmap 缩放时支持采用双线性缩放算法（C-API）。

**IME Kit**：新增支持通过 C API 调用输入法的相关能力。

**Map Kit**：支持设置地图元素压盖顺序。支持根据路由地进行坐标纠偏。

**Media Kit**：新增 API 支持 AVPlayer 播放多音轨的视频时选择指定音轨。新增 API 支持设置音频静音或取消静音。新增 C-API 支持设置录屏时的屏幕分辨率。新增 C-API 支持对应用自身的窗口做录屏隐私保护的豁免。新增 API 支持获取视频的预览缩略图。

**Media Library Kit**：支持获取最近图片的能力。支持 PhotoPicker 缩略图和大图预览之间的联动。

**Network Kit**：新增支持通过 C-API 调用网络管理的能力。

**Network Boost Kit**：新增支持连接迁移状态订阅和去订阅功能。支持连接迁移模式设置。

**Online Authentication Kit**：支持 SOTER 免密身份认证。

**Performance Analysis Kit**：新增 API 支持获取应用进程被调试的状态。

**Scenario Fusion Kit**：新增支持实况窗订阅 Button。支持三方框架接入智能填充。

**NDK**：支持使用 JSVM-API 的 WebAssembly 接口编译 wasm module。JSVM 的内存管理支持 BackingStore 机制，可以基于 BackingStore 申请的内存创建 array buffer。

公共：配置文件 app.json5 新增 cloudFileSyncEnabled 标签，用于标识当前应用是否启用端云文件同步能力。

### 二、影响兼容性的变更说明

针对所有应用的变更：

**Ability Kit**：拉起 UIExtensionAbility 增加调用方为前台应用的校验，防止后台应用拉起 UIExtensionAbility 的风险。

借助 Want 分享文件 URI 时无权限的 URI 会被拦截：在 want 的 flags 字段设置授权 flag 前提下，对无权限的 File URI 进行拦截，保障文件分享的安全性。

**childProcessManager** 增加子进程最大数量限制：为防止恶意调用，每个应用通过 childProcessManager 启动的子进程总数上限为 512 个。

**包管理接口变更为 system API**：因安全合规要求，包管理中的 bundleManager.verifyAbc 接口以及 bundleManager.deleteAbc 接口变更为 system API，仅供系统应用调用，外部开发者调用会返回错误。

**ArkTS**：util.TextEncoder 模块 utf-16le 和 utf-16be 编码数据行为变更，使其符合标准定义。Worker 模块中 RestrictedWorker 的接口变更为 system API，外部开发者可使用 worker.ThreadWorker 类创建 worker 线程进行替代。

**AVCodec Kit**：OH_AVCodecBufferAttr 接口行为变更，用户获取的视频轨 pts 属性值不再统一从 0 开始，而是提供文件实际封装的原始 pts 信息，同时从 API12 开始支持获取轨道起始时间信息，方便用户进行转换。

**Camera Kit**：CameraPosition.CAMERA_POSITION_FOLD_INNER 接口废弃，开发者可通过监听折叠屏折叠状态变化后重新获取镜头信息的方式进行适配。

**Core File Kit**：file-access-across-devices 接口行为变更，新增主动建链接口，由应用主动触发建链，适用于同 Wi-Fi 和不同 Wi-Fi 的场景。setSecurityLabel 接口行为变更，应用数据设置过数据风险等级后，无法降低数据风险等级。getSecurityLabel 接口行为变更，获取未设置过数据风险等级标签的数据时，默认返回 “s3”。

**Distributed Service Kit**：使用 udid-hash 与 appid 和盐值基于 sha256 混淆截断后保留前 16 位值作为 deviceid 的调用行为变更，设备重置、应用卸载重装或不同应用在同一设备上获取的 deviceid 不同，设备间传递 deviceid 比较可将 networkid 发送到对端进行比较，应用将 deviceid 与 SA 获取的 deviceid 进行比较可使用 networkid。

**调试命令**：命令行调试工具使用权限缩小，部分命令仅 debug 应用可用。hdc 工具 tmode usb 命令功能废弃，可通过调测设备的开发者选项界面中 USB 调试开关控制 USB 调试通道开启和关闭。aa 工具不支持对 Release 模式的应用进行调试。

### 针对 API 12 应用的变更：

**ArkTS**：

StringDecoder 中特定场景下解码错误数据行为变更，对存在元素 0 的 Uint8Array 对象正确进行解码，解码数据完整。

**变更的接口/组件：**

util.StringDecoder 模块下的两个接口：

write(chunk: string | Uint8Array): string;

end(chunk?: string | Uint8Array): string;

```ts
import { util } from "@kit.ArkTS";

let decoder = new util.StringDecoder("utf-8");
// 0xE4, 0xBD, 0xA0 解码结果：你
// 0                解码结果：\u0000(不可见字符，占一个长度)
// 0xE5, 0xA5, 0xBD 解码结果：好
let input = new Uint8Array([0xe4, 0xbd, 0xa0, 0, 0xe5, 0xa5, 0xbd]);
const decoded = decoder.write(input);
const decodedend = decoder.end(input);

// 变更前:
// console.info("decoded:", decoded);// 你
// console.info("decoded.length:", decoded.length);// 1
// console.info("decodedend:", decodedend);// 你
// console.info("decodedend.length:", decodedend.length);// 1

// 变更后：
console.info("decoded:", decoded); // 你好
console.info("decoded.length:", decoded.length); // 3
console.info("decodedend:", decodedend); // 你好
console.info("decodedend.length:", decodedend.length); // 3
```

**ArkUI**：状态管理 V2 版本组件内的

@Local,@Param,@Event,@Provider,@Consumer,@BuilderParam 必须声明类型。因为装饰器是有类型限制的，所以这些装饰器需要加上类型校验。，新版本如果修饰的变量没有写类型编译报错。

```ts
@Builder
function testBuilder() {

}

@Entry
@ComponentV2
struct V2ComponentMember {
  @Local localValue: string = 'localValue';
  @BuilderParam builderParamValue: () => void = testBuilder;
  @Param paramValue: string = 'paramValue';
  @Event eventValue: string = 'eventValue';
  @Provider() providerValue: string = 'providerValue';
  @Consumer() consumerValue: string = 'consumerValue';
  build() {

  }
}
```

修复 C-API 场景下 NODE_TIME_PICKER_DISAPPEAR_TEXT_STYLE 的 get 接口的错误行为，若要保持原效果不变，可换用 NODE_TIME_PICKER_TEXT_STYLE 接口。

component3d 获取资源的路径格式由 Resource 类型不兼容变更到 ResourceStr 类型,既支持从 rawfile 路径读取也支持从磁盘路径读取。

不兼容场景：
当用户存在指定数据类型赋值场景时，会出现不兼容情况; 例如：

```ts
const params1: SceneResourceParameters = {
  name: "name1",
  uri: $rawfile("default_path"),
};
const test_uri: Resource | undefined = params1.uri;
```

变更的接口/组件

1、 Component3D 初始化资源接口 SceneOptions 中 sence 变量数据类型由 Resource 变更为 ResourceStr；

2、 Scene 中 uri 变量数据类型由 Resource 变更为 ResourceStr；

3、 SceneResources 中 uri 变量数据类型由 Resource 变更为 ResourceStr；

4、 component3d 中 environment、customRender、shader 和 shaderImageTexture 接口的入参由 Resource 类型变更为 ResourceStr 类型；

5、 Scene 中 load 接口的入参由 Resource 类型变更为 ResourceStr 类型；

```ts
import { Scene, Image, SceneResourceParameters, SceneResourceFactory } from '@kit.ArkGraphics3D'

const params1: SceneResourceParameters = { name: "name1", uri: $rawfile("default_path") }
// 变更前
// const test_uri: Resource | undefined = params1.uri;
// 变更后适配为
const test_uri: ResourceStr | undefined = params1.uri;

@Entry
@Component
struct node_geometry {
    scene: Scene | null = null;
    @State sceneOpt: SceneOptions | null = null;
    envImg: Image | null = null;

    onPageShow(): void {
        this.Init();
    }

    onPageHide(): void {
        if (this.scene) {
            this.scene.destroy();
        }
    }

    Init(): void {
        if (this.scene == null) {
            Scene.load($rawfile("default_path"))
            .then(async (result: Scene) => {
                this.scene = result;
                this.sceneOpt = { scene: this.scene, modelType: ModelType.SURFACE } as SceneOptions;
                let rf: SceneResourceFactory = this.scene.getResourceFactory();

                this.envImg = await rf.createImage({ name: "image1", uri: test_uri });
            });
        }
    }

    build() {
        Column() {
            Component3D(this.sceneOpt)
                .renderWidth('100%')
                .renderHeight('100%')
                .width('100%')
                .height('100%')
        }
    }
}
```

**ArkWeb**：CustomDialog 内嵌 Web 组件软键盘避让方式由改变 Web 高度避让软键盘变更为抬升 CustomDialog 避让软键盘。

CustomDialog 弹窗避让软键盘时，会将弹窗整个抬升，并且与软键盘之间存在一定的安全间距。如果应用不期望弹窗被抬升或存在安全间距，应当合理配置 CustomDialog 的属性或改用其他组件替代。

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/ed0e463291114c2b8735f9454c6455a7~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg57qv54ix5o6M6Zeo5Lq6:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjg0OTU0ODM0MjQwMzQ1NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1741679453&x-orig-sign=Jkyx0pREXjjWvRH7GYA59n6Uykg%3D)

**IME Kit**：

系统增加对基础访问模式输入法的管控，以保护用户个人数据安全。

输入法 Extension 进程使用独立沙箱，基础访问模式下受到系统管控，无法使用涉及访问或泄漏用户个人数据的接口，也不能拉起其他应用进程。开发者应在应用初始化流程中查询当前模式，调整内部功能呈现情况。

**变更前：**

- 输入法 Extension 进程与应用的主入口的进程共用同一个应用沙箱，两个进程对该沙箱均可读可写。
- 输入法 Extension 进程可以拉起其他 Extension 应用或其他应用的 UIAbility。
- 输入法 Extension 进程可以使用涉及访问或泄漏用户个人数据的接口，可以将数据传出进程。

**变更后：**

- 输入法 Extension 进程使用独立沙箱，与应用的主入口进程不可互相访问对方独立沙箱。
- 新增输入法 Extension 与应用的主入口的共享沙箱，基础访问模式下输入法 Extension 对共享沙箱只读，完整访问模式下可读可写；应用的主入口对共享沙箱保持可读可写。

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/7b2d7605a33440b4824d711fe62b5140~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg57qv54ix5o6M6Zeo5Lq6:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjg0OTU0ODM0MjQwMzQ1NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1741679453&x-orig-sign=2BX37d9%2B4Th1gj6m7u%2BCogF9VQY%3D)

- 基础访问模式下，输入法应用 Extension 进程无法拉起其他 Extension 应用进程以及其他 UIAbility。
- 基础访问模式下，输入法 Extension 进程会受到系统管控，不能使用涉及访问或泄漏用户个人数据的各种接口，同时无法将数据传递出进程。管控功能包括但不限于：网络、短信、电话、麦克风、定位、相机、蓝牙、壁纸、支付、日历、游戏、扬声器、Wi-Fi、剪切板、多媒体、联系人、公共事件、系统账号、健康数据、地图服务、推送服务、融合搜索、共享内存、分布式特性、广告设备标识等。
- 基础访问模式下，输入法 Extension 可以使用基础输入功能相关的必要系统能力，例如，IME Kit、ArkUI、窗口、图形、屏幕管理等。

为确保输入法应用在运行期间功能正常，建议输入法应用在应用初始化流程：onCreate 回调中，通过调用接口 getSecurityMode 查询当前模式：

若当前处于基础模式，可以调整内部功能呈现情况，防止出现功能不可用。

若当前处于完整模式，可以使用涉及访问用户数据的接口，但是，对于这些接口的访问仅限于提升输入法体验。
为保证输入法功能稳定，需要确保在基础模式下仅使用与基础输入功能相关的能力，不能试图通过绕过系统机制将数据传递到输入法 Extension 进程和沙箱外。

以上就是关于鸿蒙 next 的功能拓展以及 API12 的一些变革创新，鸿蒙正在以一种不可思议的速度快速生根发芽，我期待它成为参天大树的那一天，也期待大家一起参与进来，为鸿蒙的普及以及生态添砖加瓦。

好了，今天就说到这里，后面我们就要正式进入鸿蒙 next 的开发了，很高兴能与大家一起进步，共同成长，一起见证鸿蒙。
