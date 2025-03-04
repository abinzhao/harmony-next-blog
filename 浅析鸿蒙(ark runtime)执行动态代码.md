> 众所周知，鸿蒙 next 的应用运行在 ark runtime 下，而 ark runtime 是驱动着鸿蒙应用的基石。ark runtime 支持动态类型和静态类型在内的多种编程语言（目前支持 js, ts, arkTs 等，与传统 js runtime 不同的是，ark compiler 工具链在编译 ts 源码时，不会像传统 js 引擎，先将 ts 转为 js 代码，再交给 js runtime 执行，而是在编译 ts 源码时，会分析推导类型信息，在运行前即可预生成内联缓存从而加速字节码执行）。详细的代码实现以及官方文档大家可以去这里看：[arkcompiler 仓库](https://gitee.com/openharmony/arkcompiler_ets_runtime/tree/master)。
>
> 整体上看，ark runtime 仍然是符合 Ecmascript 规范的 js 虚拟机实现。既然是 js 虚拟机实现，那大家一定会想到这样执行远程动态代码下发/热修等场景在鸿蒙上岂不是可以遍地开花，分分钟绕过 App Gallary 的代码签名/审核随意更新客户端代码！

## Eval / new Function

提起使用 js 动态执行代码，大家第一个想起的一定是**eval**。

Eval 作为 js 标准规范的方法，用于执行一段 javascript 表达式、声明、脚本并返回代码段执行结果。同时使用 Function prototype 也能达到相同的效果。

果然没有那么简单，还没有编译 Dev Eco Studio 的静态检查便提示无法在 arkts 中使用标准库。
![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/66791a2408ca4718aa4dd91d229f5fe6~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg57qv54ix5o6M6Zeo5Lq6:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjg0OTU0ODM0MjQwMzQ1NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1741679426&x-orig-sign=1Lbo2Ze%2FTnZQbRBjmCokN3ioGsc%3D)

这可难不倒大家，众所周知（大声宣扬），arkts 的严格的静态检查仅适用于 ets 文件，只要把代码写在 ts 文件中，就能愉快的使用 ts/js 的大部分特性了（甚至 any，随心所欲的向任意对象中加入/删除字段、方法等）。

说干就干，代码是不报错了，程序也跑起来了，但是在调用时 app 果断崩溃，调用`eval / new Function`分别提示：

```ts
Error message:not support eval().
Error message:Not support eval. Forbidden using new Function()/Function().
```

```ts
export class InjectUtil {
  static inject(obj: any) {
    eval("1+1");
    InjectUtil.evalByFunc();
    obj.Nav = null;
  }

  static evalByFunc() {
    var fn = Function as any;
    return fn("console.log(eval!!!!!!!!!!!!!!!!!!!!!!!!!!!!)")();
  }
}
```

看来 arkcompiler 在运行时也屏蔽掉了 js 可以随意动态执行代码的特性。看来直接使用 ts 的 api 是无法达到动态执行代码的目的。

查阅官方文档，已经对 ark runtime 的这一表现作出了解释：

ArkCompiler 前端编译工具链将 ArkTS/TS/JS 程序预先静态编译为方舟字节码，并且还提供了多重混淆能力的增强，有效地提升了开发者代码资产的安全强度。另外出于安全的考虑，ArkCompiler 不支持 sloppy 模式的 JS 代码，也不支持 eval 等运行动态字符串的功能。

在查阅了鸿蒙官方文档后，发现其并未完全堵死 ark runtime 动态代码的执行能力，在官方支持上仍然提供了使用 ndk 来开发动态 js 代码执行的能力。

## JSVM-API

`JSVM-API`是鸿蒙中提供的基于标准 JS 引擎提供的一套稳定 ABI，为开发者提供了一套较为完整的 JS 引擎能力，包括创建和销毁引擎，执行 JS 代码，JS/C++交互等关键能力。相当于 v8 api 的精简版。
![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/c277f873a50140b69398f2033bcde272~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg57qv54ix5o6M6Zeo5Lq6:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjg0OTU0ODM0MjQwMzQ1NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1741679426&x-orig-sign=lEGDbOr9uFkcJNAEKRoK4bBfSJw%3D)

这里的 js engine 在官方文档上并未说明是否是 ark runtime，但基于目前 ark runtime 的开源代码，这里 js vm 的能力应该还是由 ark runtime 提供（欢迎勘误）。

受限于`JSVM-API`提供的能力，我们无法获取到主线程所在的 jsvm env（也有可能主线程根本不是 jsvm），只能通过 jsvm 创建一个全新的隔离环境，在这个 js 环境中，我们可以动态的执行 js 代码。

使用 jsvm 执行 js 脚本的主要流程:

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/29f2b466582b4533b1bbd73685347e67~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg57qv54ix5o6M6Zeo5Lq6:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjg0OTU0ODM0MjQwMzQ1NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1741679426&x-orig-sign=Rhrn1pq%2FKpk5BorCGrj9dz7wOaU%3D)

### 核心代码

```cpp
// jsvm提供的接口头文件
#include "ark_runtime/jsvm.h"

JSVM_VM* vm;
JSVM_ENV* env;

// 创建jsvm虚拟机 通过napi调用
static void createArcJsContext() {
  JSVM_Status status;

  // 初始化 可以设置vm启动参数 argv argc
  JSVM_InitOptions init_options;
  memset(&init_options, 0, sizeof(init_options));
  OH_JSVM_Init(&init_options);

  // 虚拟机实例
  // 可以设置gc相关参数、snapshot bin等
  JSVM_CreateVMOptions options;
  memset(&options, 0, sizeof(options));
  status = OH_JSVM_CreateVM(&options, vm);
  // vm scope。在jsvm中scope 即作用域，用于资源管理，在scope打开期间资源可用，scope关闭后自愿释放（RAII）
  // 类似v8中各种scope的设计，在后续使用中，各种scope可以以raii风格封装方便使用
  JSVM_VMScope vm_scope;
  status = OH_JSVM_OpenVMScope(js_vm, &vm_scope);

  // js env
  js_env = new JSVM_Env;
  status = OH_JSVM_CreateEnv(...);
  // env scope
  status = OH_JSVM_OpenEnvScope(...);
}

static napi_value EvalJS(napi_env env, napi_callback_info info) {
  ...省略...
  // 待运行js代码
  std::string scriptStr = napiValueToString(env, args[1]);
  // scope 用于资源管理
  OH_JSVM_OpenHandleScope(...);
  // 转为jsvm字符串
  OH_JSVM_CreateStringUtf8(...);
  // 编译js脚本
  OH_JSVM_CompileScript(...);
  // 运行脚本
  OH_JSVM_RunScript(...);
  // 获取执行结果（省略...）
  ...省略...
}

// 释放先前创建的各种scope 关闭vm napi调用
static void releaseResources() {
  OH_JSVM_CloseEnvScope(...);
  OH_JSVM_DestroyEnv(...);
  OH_JSVM_CloseVMScope(...);
  OH_JSVM_DestroyVM(...);
}
```

```ts
// index.d.ts 导出napi方法定义
export const createJsCore: (fun: Function) => number;
export const releaseJsCore: (a: number) => void;
export const evaluateJs: (a: number, str: string) => string;
```

```ts
// 创建首个运行环境，并绑定TS回调
const coreId = testNapi.createJsCore(MyCallback);

let sourcecodestr = `
          {
          let a = "hello World";
          consoleinfo(a);
          const mPromise = createPromise();
          mPromise.then((result) => {
          assertEqual(result, 0);
          onJSResultCallback(result, "abc", "v");
          });
          a;
          };`;

// 在首个运行环境中执行JS代码
console.log("TEST evalUateJS :   " + testNapi.evalUateJS(coreId, sourcecodestr));

// 释放运行环境
testNapi.releaseJsCore(coreId);
```

实测 js 代码片段可以成功运行编译执行，从应用的主线程调用 napi 创建 jsvm，并执行指定的一段 js 代码，在 jsvm 中创建的环境也能调用创建 vm 时传入的回调函数将结果返回主线程/主 vm。

可以正常实现动态代码和 app 主线程代码的双向调用：

主 isolate(主线程) -> napi -> 用户 isolate(jsvm)

用户 isolate(jsvm) -> napi -> 主 isolate(主线程)

### 优点

- js 执行环境与主线程环境隔离，通过 napi 进行中转通讯，在 jsvm 环境中执行代码不会影响到主线程代码执行，相对安全。

### 缺点

- 需要手动管理 jsvm、相关 vm env 等，使用较为繁琐，应用前需要对 js vm api 进行封装。
- 动态执行的 js 代码不能使用 import 等，需为完整逻辑代码。
- 不能调用主 vm 中中相关对象/api。
- 未经编译 arc compiler 编译优化，可能会有性能损失。
- 需要写大量的 binding 相关的胶水代码，繁杂的工作量较大。

## NAPI

node 的 napi 将 v8 底层数据结构全部黑盒化，抽象为统一的接口，使得开发者无需再使用 v8 相关 api 操作，与 v8 解耦，使开发者不会遇到由于 v8 引擎变更或 node 版本升级而重新开发的风险。

在鸿蒙中也实现了 napi 相关规范与接口定义，使开发者可以以 napi 规范来开发鸿蒙应用的 native 扩展。

### napi_run_script

napi_run_script 是 napi 标准规范中执行 js 代码的函数，依赖于 napi，该方法的调用比较简单，直接上运行代码及调试结果

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/cb80ca75792e4cb9aee3fe3357f3ed1b~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg57qv54ix5o6M6Zeo5Lq6:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjg0OTU0ODM0MjQwMzQ1NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1741679426&x-orig-sign=fTMAWdpkSf%2FumUZRSv%2BwAXE8FB8%3D)
在鸿蒙中 napi_run_script 调用可以成功完成，也返回了 napi_ok 成功状态的返回码，但是实际上代码并没有执行，脚本运行返回值也被置为 null。

查阅官方文档，并没有对 napi_run_script 这个接口做出说明，猜测 ark runtime 屏蔽了或未实现这个接口。

### napi_run_script_path

针对这个接口，鸿蒙官方有给相关说明：[napi_run_script_path 官方文档](https://developer.huawei.com/consumer/cn/doc/harmonyos-faqs-V5/faqs-ndk-65-V5)。文档中提到可以使用该接口执行 abc 格式的文件（arkts 编译后生成的字节码产物），但没有给出操作流程。摸索了下，鸿蒙 sdk 中的 es2abc 工具可以实现这个目标：
/Applications/DevEco-Studio.app/Contents/sdk/HarmonyOS-NEXT-DB3/openharmony/ets/build-tools/ets-loader/bin/ark/build-mac/bin/es2abc

用法（注意，使用 macos 的同学需要将 es2abc 这个文件拷贝出.app， 否则会因为权限不足的原因拷贝失败）：

    ./es2abc test.js

将生成的 test.abc 文件拷贝至 entry 的 rawfile 中，调用即可执行对应的代码。

```js
napi_run_script_path(env, "/entry/resources/rawfile/test.abc", &result);
```

附上测试脚本 test.ets：

```ts
console.log("hello world from dynamic code");
console.log(globalThis);
globalThis.Cornerstone.hello();
```

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/774927470aa647dc9dce9fa907789db7~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg57qv54ix5o6M6Zeo5Lq6:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjg0OTU0ODM0MjQwMzQ1NCJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1741679426&x-orig-sign=kQI3VZwlVrR%2BQ7VriIr8Fht7FdI%3D)
可以发现动态下发的代码可以正常调用到我们在程序运行时向 globalThis 中注入的 Cornerstone 对象，说明动态执行的代码与 app 运行的主线程代码处于同一 vm 环境。

### 优点

使用 napi 执行动态代码，无需手动管理 vm 虚拟机的生命周期，同时可以复用并调用到主 vm 环境中已存在的对象，更加灵活友好。

可享受到.abc 带来的性能优化。

但同时下发的 abc 文件也与 jsvm 的限制一致，无法使用 import，需将动态代码中的相关依赖提前注入到 vm 环境/携带完整逻辑等。

### 缺点

因为执行环境与 app 主 vm 一致，恶意代码可随意破环主 vm 中相关内存数据。

## 总结

出于安全考虑鸿蒙（ark runtime）在 ts 层面对动态执行代码作出了诸多限制，但是我们仍能通过 napi / jsvm 的方式达到动态执行代码的目的，尤其是 napi，可在主 vm 环境中执行动态代码，并且能享受到方舟编译器所带来的性能优化。
