[README_部署步骤.md](https://github.com/user-attachments/files/34873756/README_.md)
# meter-scan# GitHub + jsDelivr 部署扫描页（5 分钟，免费，非洲有节点）

> 目标：把 `scan.html` 从国内 CDN（aka.doubaocdn.com，坦桑打开慢、每次 302 不缓存）搬到 jsDelivr 全球边缘 CDN（非洲有节点，固定短链，浏览器可缓存，秒开）。

## 三步部署

### 第 1 步：建 GitHub 公开仓库

1. 打开 https://github.com/new
2. Repository name：`meter-scan`（或任意名字，全小写）
3. 选 **Public**（必须公开，jsDelivr 才能访问）
4. 勾选 "Add a README file"（可选，方便初始化）
5. 点 **Create repository**

### 第 2 步：上传 scan.html

1. 在仓库页面点 **Add file → Upload files**
2. 把本目录的 `scan.html` 拖进去
3. 点 **Commit changes**（直接提交到 main 分支）

### 第 3 步：获取 jsDelivr 链接

你的扫描页地址就是：

```
https://cdn.jsdelivr.net/gh/你的GitHub用户名/仓库名/scan.html
```

例如用户名是 `johnsmith`，仓库名是 `meter-scan`，则链接是：

```
https://cdn.jsdelivr.net/gh/johnsmith/meter-scan/scan.html
```

> jsDelivr 会自动缓存，全球边缘节点加速。以后更新 scan.html 后，用 `https://cdn.jsdelivr.net/gh/用户/仓库/scan.html?vers=2` 强制刷新缓存。

## 第 4 步：更新 Apps Script 代码

1. 打开 `电表系统_一键版_v39.gs`（我已生成，SCAN_PAGE 留了占位符）
2. 找到第 1 行附近的 `var SCAN_PAGE = '@@YOUR_JSDELIVR_URL@@';`
3. 把 `@@YOUR_JSDELIVR_URL@@` 替换成你的 jsDelivr 链接（第 3 步拿到的）
4. 按部署步骤：粘贴 → Ctrl+S → 运行 setupSheets → 部署新版本

## 验证

手机打开 `/exec` → 点 📹 连续扫码按钮 → 应秒开扫描页（不再转圈加载）。页面底部应显示 "jsDelivr CDN" 字样（我在 scan.html 里加了标识）。

## 回滚

如果 jsDelivr 有问题，把 SCAN_PAGE 改回 `https://aka.doubaocdn.com/s/1R438P1043` 重新部署即可。

---

## scan.html 优化点（相比旧版）

1. **二维码优先识别**：FORMATS 列表把 qr_code 放最前（表上如有二维码秒解）
2. **强制 1080p**：4 级降级请求（1080p 精确 → 1080p 理想 → 720p → 默认），条码占比够大才稳
3. **5 个取样带**（旧版 3 个）：从宽到窄覆盖不同距离/大小的条码，近距远距都能解
4. **解码频率提升**：90ms/帧（旧版 110ms），更快命中
5. **连续失败自动放大**：25 帧未识别自动放大一级（支持变焦的手机）
6. **防重复加入**：1.5 秒内同一块表不重复加入批量清单
7. **取景框加宽**：左右 4%（旧版 6%），高度 24%（旧版 18%），引导条码撑满
8. **零外链**：除备用引擎 zxing-wasm（仅 iPhone/微信需要时才下载），页面本身零依赖，13KB
