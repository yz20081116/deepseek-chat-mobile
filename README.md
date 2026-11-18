# DeepSeek Chat 安卓版

把 [deepseek-ai/chat-demo](https://github.com/deepseek-ai/chat-demo) 的静态构建产物用 Capacitor 包成一个独立 APK。

- 网页资源**全部内置**在安装包里，App 打开时不访问任何网站，不需要服务器、不需要域名
- 对话记录和 API Key 保存在 **App 私有目录**里，其他 App 和浏览器都读不到
- 桌面图标、独立窗口，全程没有浏览器地址栏
- 只在你自己的手机上侧载安装，不经过应用商店

## 目录结构

```
www/                       # 网页资源（index.html + static/），来自官方 v0.1.1 构建
assets/icon.ico            # 图标源文件，构建时转成安卓各尺寸图标
capacitor.config.json      # Capacitor 配置（appId / 应用名 / webDir）
package.json               # 依赖：@capacitor/core · cli · android
.github/workflows/build-apk.yml   # 云端构建流程，产出 app-debug.apk
```

`android/` 目录**不提交**，由构建流程现场生成，所以这里看不到它。

## 怎么构建（不需要装任何开发工具）

### 1. 建仓库

登录 GitHub，右上角 **+ → New repository**，名字填 `deepseek-chat-mobile`，
Public 或 Private 都行，**不要**勾选 "Add a README file"，然后 Create。

### 2. 上传文件

在新建的空仓库页点 **uploading an existing file**，把本文件夹 `deepseek-chat-mobile`
里**所有内容**拖进去（是里面的内容，不是文件夹本身）。

⚠️ `.github` 是隐藏文件夹，网页拖拽有时会漏掉它。上传完成后**检查仓库根目录有没有 `.github` 文件夹**：

- 有 → 直接下一步
- 没有 → 点 **Add file → Create new file**，文件名输入 `.github/workflows/build-apk.yml`，
  把本目录同名文件的内容整段粘贴进去，Commit

### 3. 等构建

推送后会自动开始。点仓库顶部 **Actions** 标签，看到 `Build Android APK` 在跑。
首次约 8–12 分钟（要下 Gradle、Android SDK、依赖），之后有缓存会快一些。
等它变成绿色对勾。

### 4. 下载 APK

点进那次运行记录，页面底部 **Artifacts** 区域，下载 `deepseek-chat-apk.zip`，
解压得到 `app-debug.apk`。

### 5. 装到手机

把 APK 传到手机（微信文件传输助手 / QQ / 数据线都行），点击安装，
系统会提示"未知来源应用"，允许即可。

装完桌面就有图标了，点开直接进聊天界面，第一次进去在设置里填 API Key。

## 数据与备份

- 数据位置：App 私有数据目录，卸载 App 才会清空
- 升级安装（签名一致）会保留数据
- **签名提醒**：构建流程用 `actions/cache` 缓存了一个固定的 debug 签名，正常情况下多次构建签名一致，
  可以直接覆盖安装。如果缓存过期（约 7 天不活动）导致重新生成签名，覆盖安装会失败，
  这时只能卸载重装 —— **卸载会清空记录**，所以更新前先在 App 设置里导出历史备份。

## 已知限制

- 语音输入依赖 WebView 的麦克风权限，打包后可能不可用；其余功能（流式回复、深度思考、搜索、
  附件、公式、图表、导入导出）不受影响
- 打包的是官方 demo 的前端，接口仍指向 `api.deepseek.com`，消耗你自己的 API 余额
- 仅供个人使用，未上架，未做应用签名保护
