# Tauri + Next.js 桌面应用

一个使用 Tauri 2.0 和 Next.js 15 构建的跨平台桌面应用程序示例。

## ✨ 特性

- 🚀 **现代技术栈**: Tauri 2.0 + Next.js 15 + TypeScript
- 🎨 **美观界面**: Tailwind CSS 样式系统
- ⚡ **高性能**: Rust 后端 + React 前端
- 📦 **轻量级**: 相比 Electron 体积更小
- 🔒 **安全**: Tauri 提供的安全沙箱环境
- 🌍 **跨平台**: 支持 macOS、Windows、Linux

## 📸 预览

应用显示一个简洁的欢迎页面，包含渐变背景和响应式设计，支持浅色/深色模式。

## 🛠️ 技术栈

### 前端
- **框架**: Next.js 15 (App Router)
- **语言**: TypeScript
- **样式**: Tailwind CSS 4
- **构建**: Turbopack

### 后端
- **框架**: Tauri 2.0
- **语言**: Rust
- **打包**: macOS (.app, .dmg), Windows (.exe, .msi), Linux (.deb, .AppImage)

## 📋 前置要求

- Node.js 20+
- Rust (通过 rustup 安装)
- 操作系统相关依赖：
  - **macOS**: Xcode Command Line Tools
  - **Windows**: Microsoft Visual C++ Build Tools + WebView2
  - **Linux**: 见 [Tauri 文档](https://tauri.app/start/prerequisites/)

## 🚀 快速开始

### 1. 克隆项目

```bash
git clone <repository-url>
cd my-tauri-app
```

### 2. 安装依赖

```bash
npm install
```

### 3. 开发模式

```bash
npm run tauri dev
```

这将启动 Next.js 开发服务器并打开 Tauri 应用窗口。支持热重载。

### 4. 生产构建

```bash
npm run tauri build
```

构建产物位置：
- **macOS**: `src-tauri/target/release/bundle/macos/`
- **Windows**: `src-tauri/target/release/bundle/msi/`
- **Linux**: `src-tauri/target/release/bundle/deb/`

## 📁 项目结构

```
my-tauri-app/
├── app/                    # Next.js 应用目录
│   ├── page.tsx           # 主页面组件
│   ├── layout.tsx         # 根布局
│   └── globals.css        # 全局样式
├── public/                # 静态资源
├── src-tauri/             # Tauri 后端
│   ├── src/               # Rust 源代码
│   │   ├── main.rs       # 主入口
│   │   └── lib.rs        # 库文件
│   ├── icons/            # 应用图标（多尺寸）
│   ├── Cargo.toml        # Rust 依赖配置
│   └── tauri.conf.json   # Tauri 应用配置
├── next.config.ts         # Next.js 配置
├── package.json           # Node.js 依赖
├── SETUP.md              # 详细搭建指南
└── README.md             # 本文件
```

## ⚙️ 配置说明

### Next.js 配置

项目使用静态导出模式（`output: 'export'`），因为 Tauri 不支持 Next.js 的服务端渲染。

```typescript
// next.config.ts
const nextConfig: NextConfig = {
  output: 'export',  // 静态导出
  images: {
    unoptimized: true,  // 禁用图片优化
  },
};
```

### Tauri 配置

应用标识符和窗口配置在 `src-tauri/tauri.conf.json` 中：

```json
{
  "identifier": "com.mytauriapp.app",
  "build": {
    "frontendDist": "../out"
  },
  "app": {
    "windows": [{
      "title": "My Tauri App",
      "width": 800,
      "height": 600
    }]
  }
}
```

## 🐛 常见问题

### 构建后显示旧内容

清理缓存并重新构建：

```bash
rm -rf out .next
npm run build
npm run tauri build
```

### 端口占用

Next.js 会自动选择可用端口（3000 或 3001）。

### 首次编译时间长

Rust 首次编译需要下载和编译依赖，这是正常的。后续编译会快很多。

详细问题排查请参考 [SETUP.md](./SETUP.md)。

## 📚 学习资源

- [Tauri 官方文档](https://tauri.app)
- [Next.js 文档](https://nextjs.org/docs)
- [Tauri + Next.js 集成指南](https://tauri.app/start/frontend/nextjs/)

## 📖 详细文档

完整的搭建步骤和配置说明请查看 [SETUP.md](./SETUP.md)。

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

## 📄 许可证

本项目采用 [MIT 许可证](./LICENSE)。

## 🙏 致谢

- [Tauri](https://tauri.app) - 强大的桌面应用框架
- [Next.js](https://nextjs.org) - React 框架
- [Tailwind CSS](https://tailwindcss.com) - 实用优先的 CSS 框架

---

使用 ❤️ 和 [Claude Code](https://claude.com/claude-code) 构建
