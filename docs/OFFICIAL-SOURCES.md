# 官方来源与工具链

本页于 2026-10-12 核对公开来源，区分历史验收、贡献者报告和本轮网络检查。
链接可访问不等于安装包已经验收，也不代表未来长期可用。不要绕过指纹、签名或权限检查。

## 剪映 11.5.0 完整包

[Issue #3 的维护端记录](https://github.com/mcncarl/jianying-headless/issues/3#issuecomment-5978959086)
记载 2026-10-04 下载并核对过以下官方完整包；
[10 月 10 日贡献者报告](https://github.com/mcncarl/jianying-headless/issues/3#issuecomment-6091636623)
提供了同一安装身份的独立核验结果。

- [11.5.0 官方完整包](https://lf3-package.vlabstatic.com/obj/faceu-packages/Jianying_11_5_0_13199_jianyingpro_0_creatortool_nosandbox.dmg)
- 文件大小：`1519858977` 字节。
- 历史官方更新记录中的 MD5：`c8fa7fc8b590bfda882ebe4850640870`。MD5 仅作传输核对，不能代替签名与库 SHA-256。
- 发行包编号为 `13199`；应用 `CFBundleShortVersionString` 和 `CFBundleVersion` 均为 `11.5.0`，不要混淆。
- Bundle ID：`com.lemon.lvpro`；Team ID：`X2JNK7LY8J`。
- `libvideoeditor.dylib` SHA-256：`2041482a1aaeffa4d8bd69b836f8cf38807aaad8021bca410d567c59af3bccfa`。

本轮仅确认旧版直链 HTTP 200、Content-Length 与上述大小一致，没有重新下载整包、
验签或安装。历史维护端记录和贡献者核验不是本轮验收。

[官方更新接口](https://lv-api-hl.ulikecam.com/service/settings/v3/?&aid=3704&rom_version=9965&version_code=328960&channel=jianyingpro_0&device_platform=mac)
在本轮查询已返回 **11.6.0 / 13281**，不能再描述为“当前仍指向 11.5.0”。
不要把接口里的最新版自动安装成项目运行时，也不要靠拼接文件名猜测其他构建兼容性。
旧版直链失效时应停止，不以网盘、修改包或手动替换期望哈希绕过。

核对顺序：先保留现有应用和完整草稿备份，再下载到独立目录，核对大小与 MD5，
用 `hdiutil attach -readonly -nobrowse` 只读挂载，然后在仓库根目录执行：

```bash
python3 tools/runtime_report.py --app "/Volumes/<实际卷名>/VideoFusion-macOS.app"
```

只有身份、库哈希、严格签名和 Team ID 均匹配，才另行决定是否安装。
新版保存过的草稿不保证兼容旧版；不要覆盖已有应用或草稿来尝试本流程。
安装后仍需 `doctor`、新手检查及独立草稿原生验收。此来源说明不新增 11.6.0 或其他构建支持。

## CLT 26.5 独立解包路线

[Issue #2 的 10 月 10 日贡献者报告](https://github.com/mcncarl/jianying-headless/issues/2#issuecomment-6093666287)
绑定源码 `42b3d75b15bd9f3a9bb4c11b2f5205f7b4312252`，称在 macOS 26.7 / arm64
使用 Apple 官方 CLT 安装包独立解包，得到如下精确工具链与固定产物：

| 项目 | 必须匹配 |
| --- | --- |
| 编译器 | Apple clang 21.0.0 (`clang-2100.1.1.101`) |
| SDK | 26.5 |
| 链接器 | 1267 |
| 最低系统 | 26.0 |
| codec SHA-256 | `b6533eb5eb1eea58dfa74fb1d16d3bb580970fe881f587605d358af1745f971d` |

报告使用的 [Apple CDN 安装包](https://swcdn.apple.com/content/downloads/09/08/047-91568-A_Y1CFZWQCD4/4xekpyz43i26dbp4enxfro8eb1q7wiujh5/CLTools_Executables_Universal.pkg)
本轮返回 HTTP 200、Content-Length `775780305`。本轮没有下载或验证该 pkg 的 Apple 签名，
也没有重新解包、编译，因此这里只记录可复核的候选来源，不将其称为维护端已验工具链。

下载后先用 `pkgutil --check-signature` 核对 Apple 签名，再用 `pkgutil --expand-full`
解包到新的独立目录；不运行安装器、不覆盖系统 CLT、不修改全局 `xcode-select`。
从解包树中确认实际 Developer 目录及其 SDK，而不是直接把解包根目录当成 Developer 目录。
在仓库根目录显式指定它：

```bash
python3 tools/build_native_codec.py --developer-dir "<解包后的实际 Developer 目录>" --check-toolchain
python3 tools/build_native_codec.py --developer-dir "<解包后的实际 Developer 目录>" --rebuild-check
```

只有 `toolchain-matched` 和 `reproduced-and-verified`、完整固定哈希均匹配才算构建复现。
`--rebuild-check` 不安装组件，也不代表草稿原生验收通过。
CLT 26.5 名称相同但具体编译器、SDK 或链接器不符时仍应拒绝；不要修改 pin。
提交复核材料只需源码 SHA、脱敏工具链报告与产物 SHA，不上传官方二进制。
