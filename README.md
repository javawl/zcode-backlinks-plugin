# zcode-backlinks-plugin

独立 ZCode 插件：把外链发布全流程（Supermanager 批次与租约、Cloud Mail 验证邮箱、专用持久浏览器、发布技能与站点剧本）以**零侵入**方式带入官方 ZCode——不需要 fork harness，安装本插件即可。

从 [ZCode-Link（LinkAgent）fork](https://github.com/javawl/ZCode-Link) 抽出为独立仓库，能力与 [dsh-backlinks-plugin](https://github.com/javawl/dsh-backlinks-plugin)（DeepSeek Harness 线）同源。

## 插件内容

| 组件 | 说明 |
| --- | --- |
| MCP `backlinks` | 10 个动作：batch_list / batch_get / batch_claim / lease_heartbeat / lease_release / item_result / badge_add / source_tags / mailbox_create / mail_wait |
| MCP `backlinks_browser` | 持久 Chrome 专用浏览器：navigate / snapshot / click / fill / select / press / upload / screenshot / waitFor / bringToFront / closePage + tabs / status / close / hold |
| MCP `backlinks_status` | 只读脱敏配置与准备状态 |
| 技能 `backlink-publish` | 发布流水线主技能 + 6 类站点 playbook（directory/blog/forum/article/gaming/tools）+ 证据/安全/状态/Google 会话参考 |
| 技能 `google-session` | 人工播种 Google 登录会话（密码不经智能体） |
| 命令 `/backlink-publish` | 斜杠命令入口 |
| 子代理 `backlink-publisher` | 发布专员人格与工具集 |
| `dist/configure.js` | stdin JSON 配置入口（凭据不进命令行参数、不进模型上下文） |

## 安装（本地市场）

1. ZCode → 插件市场 → 添加 → 添加插件市场，粘贴本仓库根目录（含 `marketplace.json`）。
2. 个人 → 本市场 → backlinks → 安装，并在 设置 → 插件 显式启用（插件默认关闭）。
3. 若此前启用过 `backlinks@zcode-plugins-official`（LinkAgent 时代遗留缓存），先在同一入口禁用，避免双份技能/工具。

预构建产物（`dist/`、`runtime/playwright-core/`）已提交，安装无需编译、无需全局 Node。

## 配置

配置唯一持久化于 `<数据目录>/.zcode/v2/backlinks.json`（原子写、权限 0600，token 只写不回显），也可用环境变量 fallback：

```
ZCODE_BACKLINKS_SUPERMANAGER_BASE_URL / ZCODE_BACKLINKS_SUPERMANAGER_TOKEN
ZCODE_BACKLINKS_CLOUD_MAIL_BASE_URL   / ZCODE_BACKLINKS_CLOUD_MAIL_TOKEN
ZCODE_BACKLINKS_MAILBOX_DOMAIN
```

命令行配置（stdin 输入 JSON）：

```sh
echo '{"supermanager":{"baseUrl":"https://…","token":"…"}}' | node packages/backlinks/dist/configure.js
```

优先级：有效持久化字段 > 环境变量。浏览器默认使用本机 Google Chrome（channel=chrome），需已安装 Chrome；持久 profile 按 workspace 分目录存放，跨运行保留登录态。

## 开发

```sh
pnpm install          # Node >= 23，pnpm >= 10
pnpm run build        # esbuild 打包 dist/ 并复制 runtime/playwright-core
pnpm run test         # node:test + tsx；浏览器集成测试在本机有 Chrome 时执行
pnpm run typecheck
```

仓库布局：`packages/backlinks/` 为插件本体（目录名与插件名一致）；`packages/runtime/`（`@zcode/backlinks`）为共享运行时库，构建时打进 MCP bundle。改代码后重新 `pnpm run build` 并提交更新后的 `dist/` 与 `runtime/` 产物，再走市场刷新更新。
