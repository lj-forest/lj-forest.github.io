```
layout: post
title: "如何在unbutu上部署deepseek harness？"
date: 2026-10-11 12:03:00 +0800
categories: 教程
```

# 如何在unbutu上部署deepseek harness？

### 🛠️ 第一步：安装 Node.js 22

DeepSeek Harness 要求 Node.js 版本为 22 或更高。推荐使用 NodeSource 官方源安装，这是最稳定、最推荐的方式。

在终端中依次执行以下命令：

```
# 1. 更新软件包索引
sudo apt update

# 2. 添加 NodeSource 22.x 源
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -

# 3. 安装 Node.js 和 npm
sudo apt install -y nodejs

# 4. 验证版本
node -v   # 应输出 v22.x 或更高
npm -v    # 应输出 10.x 或更高
```

------

### 📦 第二步：安装 pnpm 11

**1. 配置 npm 国内镜像源（加速下载）**

先把 npm 的下载源换成国内的，能大大提升安装速度和成功率。

```
npm config set registry https://registry.npmmirror.com
```

**2. 使用 npm 全局安装 pnpm**

执行以下命令，通过 npm 来安装指定版本的 pnpm：

```
sudo npm install -g pnpm@11
```

> **注意**：因为要全局安装，所以需要加 `sudo` 来获取权限。

**3. 验证安装**

```
pnpm -v
```

如果看到类似 `11.x.x` 的版本号，就说明安装成功了。

------

### 🚀 第三步：部署 DeepSeek Harness

有两种方式可以启动 DeepSeek Harness，推荐使用 **npx 一条命令启动**，这是最快的方式。

#### ✅ 方式 A：npx 一键启动（推荐）

```
npx @deepseek-ai/dsh web
```

这个命令会自动下载最新版本的 DeepSeek Harness 并启动 Web UI。**首次运行需要下载依赖，请耐心等待 1~3 分钟。**

> 再次启动也使用这条命令

如果希望禁止自动打开浏览器（虚拟机中通常没有图形界面），可以加上 `--no-open` 参数：

```
npx @deepseek-ai/dsh web --no-open
```

启动成功后，终端会打印访问地址，默认为 `http://127.0.0.1:3080`。

------

### 🔑 第四步：配置 API 密钥

Web UI 启动后，默认没有配置任何模型，需要添加 DeepSeek 官方 API 密钥。

1. **获取 API Key**：访问 [platform.deepseek.com](https://platform.deepseek.com/) 注册并创建 API Key。
2. **在 Web UI 中配置**：
   - 在浏览器中打开 `http://<虚拟机IP>:3080`（如果从宿主机访问，需要将虚拟机网络设为桥接模式，并使用虚拟机的 IP）。
   - 首次打开页面时，会弹出“添加一个 API Key 开始使用”对话框，将 API Key 粘贴进去，点击“保存并继续”即可。
   - 或者，打开左侧边栏 → **Settings → Models**，在 DeepSeek 卡片中填入 API Key 并保存。

> **安全提示**：API Key 会被安全地存储在 `$DSH_HOME/.credentials.yaml` 中，不会明文出现在普通配置文件中。

------

### ✅ 第五步：验证部署

配置完成后，回到主界面，选择工作区，尝试发出第一个任务：

> “帮我计算 (3+5)*2”

如果 Agent 正常调用工具并返回结果，说明部署成功。



> 以上内容均有deepseek生成