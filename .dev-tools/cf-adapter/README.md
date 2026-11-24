# cf-adapter（eucli-code-factory 契约 3 适配器）

为 `eucli-studio` 实现 `eucli-code-factory` 契约 3（`.dev-tools/adapter.toml`）要求的 7 条命令，零第三方依赖：

- `deps`：按 `package-lock.json` 使用本空间 `EUCLI_CF_DEPS_STORE/npm` 缓存执行 `npm ci`，并下载 `backend-go` 的 Go 模块至 `EUCLI_CF_DEPS_STORE/go/pkg/mod`。
- `build`：以 `CARGO_TARGET_DIR=${EUCLI_CF_BUILD_DIR}/cargo-target` 执行正式构建（前端 + Go sidecar + Tauri release exe），将完整可运行 `package/` 装配到 `EUCLI_CF_BUILD_DIR/package/` 并清理工作树中间产物。
- `start`：校验注入的实例身份后缀与数据根，以 `--fw-launched --fw-action hide` 在后台启动隔离实例（独立单实例锁、独立 WebView2 目录与独立数据目录）。
- `health`：读取 `fw-app-control-ready` 帧并向控制端口发送受鉴权探测确认实例就绪。
- `stop`：通过控制端口发送 `close` 走统一优雅关停（超时则按进程树清理），幂等返回成功。
- `seed`：本桌面视窗无需预置基线数据，按契约返回 `not-applicable`。
- `test`：校验运行中隔离实例后，依次运行版本一致性检查、TypeScript 类型检查、构建脚本与前端单测、Go 后台测试及 Rust 壳单元测试。
