# Tauri + Next.js 项目搭建指南

## 项目概述

这是一个使用 Tauri 2.0 + Next.js 15 构建的桌面应用程序，展示 "Welcome, this is hello world!" 欢迎信息。

## 环境要求

- Node.js (v20+)
- npm 或其他包管理器
- Rust (通过 rustup 安装)
- 系统依赖（macOS 需要 Xcode）

## 搭建步骤

### 1. 创建 Next.js 项目

```bash
npx create-next-app@latest my-tauri-app --typescript --tailwind --eslint --app --no-src-dir --import-alias "@/*"
```

选择配置：
- TypeScript: Yes
- Tailwind CSS: Yes
- ESLint: Yes
- App Router: Yes
- Turbopack: No (可选)

### 2. 安装 Tauri CLI

```bash
cd my-tauri-app
npm install -D @tauri-apps/cli@latest
```

### 3. 初始化 Tauri

```bash
npx tauri init --ci \
  --app-name my-tauri-app \
  --window-title "My Tauri App" \
  --frontend-dist ../out \
  --dev-url http://localhost:3000 \
  --before-dev-command "npm run dev" \
  --before-build-command "npm run build"
```

### 4. 配置 Next.js 静态导出

修改 `next.config.ts`：

```typescript
import type { NextConfig } from "next";

const isProd = process.env.NODE_ENV === 'production';
const internalHost = process.env.TAURI_DEV_HOST || 'localhost';

const nextConfig: NextConfig = {
  // 使用静态导出模式
  output: 'export',
  // 禁用图片优化（SSG 模式要求）
  images: {
    unoptimized: true,
  },
  // 配置资源前缀
  assetPrefix: isProd ? undefined : `http://${internalHost}:3000`,
};

export default nextConfig;
```

### 5. 配置 Tauri

修改 `src-tauri/tauri.conf.json`：

```json
{
  "identifier": "com.mytauriapp.app",
  "build": {
    "frontendDist": "../out",
    "devUrl": "http://localhost:3000",
    "beforeDevCommand": "npm run dev",
    "beforeBuildCommand": "npm run build"
  }
}
```

**注意**: 将 `identifier` 改为唯一值，不能使用默认的 `com.tauri.dev`。

### 6. 添加 Tauri 脚本

修改 `package.json`，添加 tauri 脚本：

```json
{
  "scripts": {
    "dev": "next dev --turbopack",
    "build": "next build --turbopack",
    "start": "next start",
    "lint": "eslint",
    "tauri": "tauri"
  }
}
```

### 7. 创建欢迎页面

修改 `app/page.tsx`：

```tsx
export default function Home() {
  return (
    <div className="flex items-center justify-center min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 dark:from-gray-900 dark:to-gray-800">
      <main className="text-center">
        <h1 className="text-6xl font-bold text-gray-800 dark:text-white mb-4">
          Welcome
        </h1>
        <p className="text-2xl text-gray-600 dark:text-gray-300">
          This is hello world!
        </p>
      </main>
    </div>
  );
}
```

## 运行和构建

### 开发模式

```bash
npm run tauri dev
```

这会：
1. 启动 Next.js 开发服务器（端口 3000 或 3001）
2. 编译 Rust 代码
3. 打开 Tauri 应用窗口

### 生产构建

```bash
npm run tauri build
```

构建产物位置：
- macOS: `src-tauri/target/release/bundle/macos/my-tauri-app.app`
- DMG: `src-tauri/target/release/bundle/dmg/my-tauri-app_0.1.0_aarch64.dmg`

## 项目结构

```
my-tauri-app/
├── app/                    # Next.js App Router
│   ├── page.tsx           # 主页面
│   ├── layout.tsx         # 布局
│   └── globals.css        # 全局样式
├── public/                # 静态资源
├── src-tauri/            # Tauri 后端
│   ├── src/              # Rust 源代码
│   ├── icons/            # 应用图标
│   ├── Cargo.toml        # Rust 依赖
│   └── tauri.conf.json   # Tauri 配置
├── out/                  # Next.js 静态导出输出（构建时生成）
├── next.config.ts        # Next.js 配置
├── package.json          # Node 依赖
└── tsconfig.json         # TypeScript 配置
```

## 关键配置说明

### Next.js 配置要点

1. **静态导出**: `output: 'export'` - Tauri 不支持服务端渲染
2. **图片优化**: `images.unoptimized: true` - SSG 模式要求
3. **资源前缀**: 开发模式下正确解析资源

### Tauri 配置要点

1. **frontendDist**: 指向 Next.js 的输出目录 `../out`
2. **identifier**: 必须是唯一的应用标识符
3. **beforeBuildCommand**: 构建前先执行 Next.js 构建

## 常见问题

### 1. 端口被占用

如果端口 3000 被占用，Next.js 会自动使用 3001。

### 2. Bundle Identifier 警告

避免使用以 `.app` 结尾的 identifier，会与 macOS 应用包扩展名冲突。建议改为：
```json
"identifier": "com.mytauriapp.desktop"
```

### 3. 首次编译时间长

Rust 首次编译会下载和编译大量依赖，这是正常的，后续编译会快很多。

### 4. 构建后显示旧内容或默认页面

**问题现象**：
修改了 `app/page.tsx` 后构建应用，但打开应用仍显示 Next.js 默认页面或旧内容。

**原因**：
Next.js 的构建缓存（`.next` 目录）和输出目录（`out` 目录）中可能保留了旧的内容。

**解决方案**：
清理缓存并重新构建：

```bash
# 清理缓存和输出目录
rm -rf out .next

# 重新构建 Next.js
npm run build

# 重新构建 Tauri 应用
npx tauri build
```

**最佳实践**：
- 修改页面内容后，先运行 `npm run build` 确认 Next.js 正确构建
- 检查 `out/index.html` 文件，确认内容已更新
- 然后再执行 `npx tauri build` 构建桌面应用

**验证构建内容**：
```bash
# 查看生成的 HTML 文件内容
head -50 out/index.html

# 应该能看到你的页面内容，例如 "Welcome" 和 "This is hello world!"
```

### 5. 开发模式热重载

在开发模式下（`npm run tauri dev`），修改 React 组件会自动热重载，但如果遇到问题：

```bash
# 停止开发服务器（Ctrl+C）
# 清理缓存
rm -rf .next
# 重新启动
npm run tauri dev
```

## 参考资源

- [Tauri 官方文档](https://tauri.app)
- [Next.js 静态导出](https://nextjs.org/docs/app/building-your-application/deploying/static-exports)
- [Tauri + Next.js 配置](https://tauri.app/start/frontend/nextjs/)
