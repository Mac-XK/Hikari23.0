# Hikari23-MacXK

基于 LLVM 23.0 的 Hikari 代码混淆工具链，适用于 iOS/macOS 开发。

## 功能特性

- ✅ 控制流平坦化 (Control Flow Flattening)
- ✅ 字符串加密 (String Encryption)
- ✅ 间接跳转 (Indirect Branching)
- ✅ 虚假控制流 (Bogus Control Flow)
- ✅ 基本块分割 (Basic Block Splitting)
- ✅ 指令替换 (Instruction Substitution)
- ✅ 函数包装 (Function Wrapper)
- ✅ 反调试 (Anti-Debugging)
- ✅ 反Hook (Anti-Hooking)
- ✅ 常量加密 (Constant Encryption)

## 支持平台

- iOS (arm64)
- iOS Simulator (arm64, x86_64)
- macOS (arm64, x86_64)
- tvOS / watchOS

## 安装方法

### 方法一：直接安装

1. 下载 `Hikari.xctoolchain` 文件夹
2. 复制到 Toolchains 目录：

```bash
cp -r Hikari.xctoolchain ~/Library/Developer/Toolchains/
```

3. 重启 Xcode

### 方法二：命令行安装

```bash
# 创建目录（如果不存在）
mkdir -p ~/Library/Developer/Toolchains

# 复制 toolchain
cp -r Hikari.xctoolchain ~/Library/Developer/Toolchains/

# 验证安装
ls ~/Library/Developer/Toolchains/
```

## 使用方法

### 1. 选择 Toolchain

在 Xcode 菜单中选择：
**Xcode → Toolchains → Hikari23-MacXK**

### 2. 配置混淆选项

在 Xcode 项目中：
**Build Settings → Other C Flags** 添加混淆参数

### 3. 混淆参数说明

| 参数 | 功能 | 说明 |
|------|------|------|
| `-mllvm -enable-allobf` | 启用全部混淆 | 一键开启所有混淆功能 |
| `-mllvm -enable-cffobf` | 控制流平坦化 | 将代码控制流转换为 switch-case 结构 |
| `-mllvm -enable-strcry` | 字符串加密 | 加密代码中的字符串常量 |
| `-mllvm -enable-indibran` | 间接跳转 | 将直接跳转转换为间接跳转 |
| `-mllvm -enable-bcfobf` | 虚假控制流 | 插入虚假的控制流分支 |
| `-mllvm -enable-splitobf` | 基本块分割 | 将基本块分割成更小的块 |
| `-mllvm -enable-subobf` | 指令替换 | 将简单指令替换为等价的复杂指令 |
| `-mllvm -enable-funcwra` | 函数包装 | 将函数调用包装成间接调用 |
| `-mllvm -enable-adb` | 反调试 | 添加反调试检测代码 |
| `-mllvm -enable-antihook` | 反Hook | 添加反Hook检测代码 |
| `-mllvm -enable-acdobf` | 反类转储 | ObjC 类信息混淆 |
| `-mllvm -enable-constenc` | 常量加密 | 加密数值常量 |

### 4. 推荐配置

**基础混淆（推荐）：**
```
-mllvm -enable-cffobf -mllvm -enable-strcry -mllvm -enable-indibran
```

**中等混淆：**
```
-mllvm -enable-cffobf -mllvm -enable-strcry -mllvm -enable-indibran -mllvm -enable-bcfobf -mllvm -enable-subobf
```

**完整混淆：**
```
-mllvm -enable-allobf
```

### 5. 函数级别控制

可以使用注解控制单个函数的混淆：

```objc
// 启用混淆
__attribute((__annotate__(("fla"))))    // 控制流平坦化
__attribute((__annotate__(("bcf"))))    // 虚假控制流
__attribute((__annotate__(("sub"))))    // 指令替换
__attribute((__annotate__(("split"))))  // 基本块分割
__attribute((__annotate__(("strcry")))) // 字符串加密

// 禁用混淆
__attribute((__annotate__(("nofla"))))  // 禁用控制流平坦化
__attribute((__annotate__(("nobcf"))))  // 禁用虚假控制流
```

**示例：**
```objc
__attribute((__annotate__(("fla"))))
__attribute((__annotate__(("strcry"))))
void sensitiveFunction() {
    NSString *secret = @"MySecretKey";
    // ...
}
```

## 环境变量

也可以通过环境变量启用混淆：

```bash
export ALLOBF=1      # 启用全部混淆
export CFFOBF=1      # 控制流平坦化
export STRCRY=1      # 字符串加密
export INDIBRAN=1    # 间接跳转
export BCFOBF=1      # 虚假控制流
export SPLITOBF=1    # 基本块分割
export SUBOBF=1      # 指令替换
export FUNCWRA=1     # 函数包装
export ADB=1         # 反调试
export ANTIHOOK=1    # 反Hook
export CONSTENC=1    # 常量加密
```

## 命令行使用

```bash
# 设置 toolchain 路径
export TOOLCHAINS=Hikari21.2-MacXK

# 编译单个文件
clang -mllvm -enable-cffobf -mllvm -enable-strcry -c test.c -o test.o

# 使用 xcodebuild
xcodebuild -project MyProject.xcodeproj -scheme MyScheme TOOLCHAINS=Hikari21.2-MacXK
```

## 注意事项

1. **编译时间**：启用混淆会显著增加编译时间，建议仅在 Release 配置中启用
2. **调试困难**：混淆后的代码难以调试，建议 Debug 配置不启用混淆
3. **代码体积**：混淆会增加二进制文件大小
4. **兼容性**：某些混淆选项可能与特定代码不兼容，如遇问题可尝试禁用部分选项

## 故障排除

### 问题：Undefined symbol: ___isPlatformVersionAtLeast

**解决方案**：确保 toolchain 包含 `lib/clang/23/lib/darwin/` 目录下的运行时库

### 问题：Unknown command line argument '-enable-xxx'

**解决方案**：确保使用的是 Hikari toolchain，而不是 Xcode 默认 toolchain

### 问题：编译崩溃或超时

**解决方案**：尝试减少混淆选项，或对特定函数禁用混淆

## 版本信息

- LLVM 版本：23.0.0
- Hikari 版本：基于开源版本修改
- 构建日期：2026年4月
- 作者：MacXK

## 许可证

本项目基于 Hikari 开源版本修改，遵循原项目许可证。

## 致谢

- [Hikari](https://github.com/HikariObfuscator/Hikari) - 原始混淆器项目
- [LLVM](https://llvm.org/) - 编译器基础设施
