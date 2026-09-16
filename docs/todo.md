# TODO

## Xray setuid 升级卡死

升级已开启 TUN setuid 的 Xray 核心时，卡在管理员授权这一步不再往下走。

- 日志停在 `Xray: previous binary is setuid — authorizing in-place upgrade`
  （`src-tauri/src/core/download.rs:652`），之后再无 `core_assets`/安装完成记录，
  进程持续 `engine idle`，实测卡住 > 2 分钟。
- 授权链路：`run_privileged`（`src-tauri/src/core/macos_auth.rs:425`）依次尝试
  sudo+PTY（Touch ID）→ AEWP → osascript 密码框。尚未确认是弹窗被忽略/挡在其他窗口后面，
  还是授权流程本身卡死无响应。
- 待办：复现时确认是否真的弹出了系统授权对话框；若没有弹窗，需排查
  `run_privileged_sudo_pty`/`run_privileged_aewp`/`run_privileged_osascript` 三条路径。

## Xray 升级后连不上

升级 Xray 核心版本后代理连接失败，怀疑是协议转换（xray config 生成）与新版本不兼容。

- 相关代码：`src-tauri/src/config/xray.rs`（配置生成）。
- 待办：确认具体是哪个协议/出站类型失败，对比新旧版本 Xray 的配置 schema 差异。
