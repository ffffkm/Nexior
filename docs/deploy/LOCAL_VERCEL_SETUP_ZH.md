# Nexior 本地运行与 Vercel 部署说明

这份说明基于当前仓库的默认模式整理：

- 前端代码部署到 `Vercel`
- 登录、注册、聊天、支付和分销能力默认继续使用 `AceDataCloud`
- 不需要你自己买服务器
- 不需要你自己准备模型 API Key 才能先跑通站点

## 1. 环境要求

- `Node.js 20.x`
- `Yarn Classic 1.22.x`
- `Git`
- `GitHub` 账号
- `Vercel` 账号

推荐版本：

```text
Node.js 20.20.2
Yarn 1.22.22
```

仓库根目录已经增加了 `.nvmrc`，方便你本地切到正确的 Node 版本。

## 2. Windows 本地启动

### 2.1 安装依赖

在 Windows PowerShell 下，优先使用下面这条命令：

```powershell
yarn install --ignore-scripts
```

原因是一个传递依赖 `@stellar/stellar-sdk` 的 `postinstall` 脚本使用了类 Unix 写法 `yarn setup || true`，在 Windows 下可能直接导致安装失败。这个脚本不影响 Nexior 前端运行，所以在 Windows 上忽略安装脚本是当前最稳的方式。

### 2.2 验证构建

```powershell
yarn build
```

构建成功后会生成 `dist/` 目录。

### 2.3 启动开发环境

```powershell
yarn dev
```

正常情况下会看到类似输出：

```text
Local:   http://localhost:8084/
```

然后访问：

```text
http://127.0.0.1:8084
```

## 3. 默认运行行为

当前仓库代码已经内置了默认后端地址，定义在 [`src/constants/endpoint.ts`](E:/deim/aiweb/nexior-src/src/constants/endpoint.ts)：

- `https://platform.acedata.cloud`
- `https://hub.acedata.cloud`
- `https://auth.acedata.cloud`

所以默认模式下：

- 本地运行时不需要额外配置 `VITE_BASE_URL_*`
- Vercel 部署时也可以先不配环境变量

只有当你后面想切到自己的后端服务时，才需要考虑这些环境变量：

- `VITE_BASE_URL_PLATFORM`
- `VITE_BASE_URL_HUB`
- `VITE_BASE_URL_AUTH`
- `VITE_WALLETCONNECT_PROJECT_ID`

## 4. 本次已验证的结果

这份工作区已经完成了以下验证：

1. 使用 `Node 20.20.2` 和 `Yarn 1.22.22` 成功安装依赖
2. 使用 `yarn build` 成功完成生产构建
3. 使用 `yarn dev` 成功在 `8084` 端口启动
4. 本地访问后，未登录用户会正确跳转到 `AceDataCloud` 登录页
5. 登录页可见邮箱登录、手机号登录、GitHub、微信、谷歌等入口
6. 注册入口可跳转到邮箱注册页，并显示验证码/注册表单

注意：

- 真正完成一次“登录后进入聊天并发消息”，仍然需要你使用自己的真实账号或 OAuth 完成认证
- 这一步依赖第三方账号和实际登录状态，不适合在当前无账号上下文里自动代跑

## 5. Vercel 部署步骤

### 5.1 准备 GitHub 仓库

推荐做法：

1. 在 GitHub 上 `Fork` `AceDataCloud/Nexior`
2. 把你自己的改动推到这个 Fork
3. 以后继续通过 `Sync fork` 跟进上游更新

### 5.2 导入到 Vercel

1. 登录 `Vercel`
2. 选择 `Add New Project`
3. 选择你自己的 `Nexior` Fork 仓库
4. 直接导入

仓库里已经有 [`vercel.json`](E:/deim/aiweb/nexior-src/vercel.json)，默认配置是：

- `installCommand`: `yarn`
- `buildCommand`: `npm run build`
- 单页应用路由回退到 `index.html`

如果 Vercel 页面需要你手动确认运行时，保持下面的思路即可：

- Node: `20.x`
- Package manager: `yarn`

### 5.3 首次上线后的验证

部署完成后，至少检查这几项：

1. `vercel.app` 默认域名能打开
2. 首页或默认入口没有白屏
3. 刷新路由不会出现 `404`
4. 未登录时能跳转到 `AceDataCloud` 登录页
5. 登录后能回到你的站点
6. 能进入聊天页面

## 6. 你没有服务器也能上线吗？

可以。

这里的结构是：

- 页面托管在 `Vercel`
- AI、登录、支付、分销等默认走 `AceDataCloud`

所以这不是“你自己搭一整套后端”的模式，而是“你托管前端站点，能力走上游服务”的模式。

## 7. 关于收款

默认模式下，不是你自己独立商户直收。

根据上游项目公开说明，这条路线更接近：

- 平台提供支付与分销体系
- 用户消费后按照分销比例返佣到上级账户
- 后续可提现

如果你后面想做成“用户的钱直接进你自己的商户账户”，那就不是当前这条原样跑通路线了，需要另外改造支付和后端。
