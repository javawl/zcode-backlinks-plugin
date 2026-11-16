# 第三方组件说明

- `packages/backlinks/runtime/playwright-core/`：Vendored playwright-core（Apache-2.0），随包原样复制，不含浏览器二进制；其 LICENSE/NOTICE 见该目录。
- `packages/backlinks/dist/mcp/server.js`、`packages/backlinks/dist/configure.js`：esbuild 打包产物，bundle 末尾（`legalComments: "eof"`）保留 @modelcontextprotocol/server、zod 等依赖的许可声明。
- 本插件业务代码（`packages/backlinks/src`、`packages/runtime/src`）为 MIT，见根目录 LICENSE。
