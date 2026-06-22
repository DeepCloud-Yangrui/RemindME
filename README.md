# RemindME

> 到时候记得提醒我！—— 一个极简的离线待办提醒 Android 应用。

## ✨ 功能

- **添加待办** — 滚轮式日期时间选择器，精确到分钟
- **历史时间轴** — 按日期分组，已完成 / 已过期一目了然
- **深色 / 浅色主题** — 一键切换，刷新不闪
- **系统级通知** — 即使 App 被彻底杀后台，到时间手机仍会弹出提醒
- **完全离线** — 所有数据保存在设备本地（localStorage），无需网络

## 🛠 技术架构

| 层级 | 技术 |
|---|---|
| UI 框架 | [Next.js 16](https://nextjs.org)（React 19） |
| 样式 | [Tailwind CSS 4](https://tailwindcss.com) + [shadcn/ui](https://ui.shadcn.com) |
| 静态导出 | `next.config.mjs` → `output: 'export'` |
| 移动端容器 | [Capacitor 8](https://capacitorjs.com) |
| 原生通知 | `@capacitor/local-notifications` |
| 本地存储 | `localStorage` |
| 图标 | [Lucide React](https://lucide.dev) |

### 为什么是静态导出？

去掉了所有后端依赖（Server Actions / PostgreSQL / drizzle），全部改为纯客户端 `localStorage`。`next build` 直接输出 `out/` 静态文件夹，由 Capacitor 加载进 Android WebView。

## 📁 项目结构

```
RemindME/
├── app/                    # Next.js App Router 页面
│   ├── layout.tsx          # 根布局 + 主题初始化脚本
│   ├── page.tsx            # 首页（导航入口）
│   ├── add/page.tsx        # 添加待办
│   ├── history/page.tsx    # 历史时间轴
│   └── settings/page.tsx   # 主题设置
├── components/             # UI 组件
│   ├── add-todo-form.tsx   # 待办表单（滚轮选择器）
│   ├── todo-item.tsx       # 待办条目（完成 / 删除）
│   ├── wheel-picker.tsx    # 滚轮选择器
│   └── theme-setting.tsx   # 深浅主题切换
├── lib/
│   ├── todos.ts            # localStorage CRUD + 原生通知调度
│   └── utils.ts            # cn() 工具函数
├── android/                # Capacitor Android 原生工程
├── capacitor.config.ts     # Capacitor 配置
├── next.config.mjs         # Next.js 静态导出配置
└── out/                    # 构建产物（已 gitignore）
```

## 🚀 本地开发

```bash
# 安装依赖
npm install

# 启动 Web 开发服务器
npm run dev
# 浏览器打开 http://localhost:3000
```

## 📱 构建 Android 应用

```bash
# 1. 静态导出
npm run build

# 2. 同步到 Android 工程
npx cap sync

# 3. 打开 Android Studio
npx cap open android
```

然后在 Android Studio 中选择模拟器或真机，点击 **Run** 即可。

## 🔔 原生通知原理

用户保存待办时，`lib/todos.ts` 中的 `addTodo` 会同时做两件事：

1. 将待办写入 `localStorage`
2. 调用 `LocalNotifications.schedule()` 向 Android 系统注册一个定时闹钟

通知由 Android 系统的 AlarmManager 驱动，即使 App 进程被系统回收，到时间仍然会弹出标题为 **RemindME**、内容为待办名称的系统通知。

## 📄 License

MIT
