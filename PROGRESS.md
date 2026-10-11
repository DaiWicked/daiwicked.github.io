# 项目进度记录

> 最后更新：2026-10-11
> 状态：进行中 — 检查子项目删除部分的恢复可行性

---

## 项目背景

- **主项目（GitHub部署版）**：`H:\daiwicked.github.io`
  - 扁平化目录结构，所有资源在根目录
  - 已删除：api/ 目录、zhongcheng/jiangjun.mp4（105MB）
  - 新增：.nojekyll、CNAME、README.md
- **子项目（原始完整版）**：`C:\Users\Administrator\Desktop\临时文件\html`
  - 有子目录结构：egg/、other/、zhongcheng/、api/
  - 包含完整的后端API和大视频文件

## 已完成工作

1. ✅ 对比两个目录结构差异
2. ✅ 梳理所有HTML文件的资源引用关系
3. ✅ 发现3处断链问题：
   - egg.html 第255行：`./egg/male-photo3.jpg` 路径错误
   - zhongcheng.html：引用 `jiangjun.mp4` 但文件已删除
   - index.html 第685行：JS跳转 `videos.html` 不存在
4. ✅ 发现5个未被引用的闲置文件：live2-cover.jpg、live3-cover.jpg、live4-cover.jpg、music-bg.jpg、logo1.png

## 当前任务 — 检查结果（已完成评估）

| 被删除内容 | 实际大小 | 恢复可行性 | 分析结论 |
|-----------|---------|-----------|---------|
| `api/get_music_list.py` | 2.3KB | ⚠️ 可保留但无法运行 | Python HTTP服务，监听8030端口，扫描 `/var/www/html/music` 返回音乐列表JSON。GitHub Pages纯静态不支持运行，仅能作为源码备份放在仓库中 |
| `api/music_api.log` | 541B | ❌ 不恢复 | 是端口占用的错误日志，已加入 .gitignore |
| `zhongcheng/jiangjun.mp4` | **100.58MB** | ⚠️ 需处理后才能提交 | 实际100.58MB，仅超GitHub 100MB硬限制0.58MB。方案：压缩/转外部托管/Git LFS |

### api/get_music_list.py 功能分析
- 基于 Python 标准库 `http.server`，无需额外依赖
- 监听 8030 端口，路径 `/api/get_music_list.py`
- 扫描 Linux 路径 `/var/www/html/music` 下的音频文件（mp3/flac/wav/ogg/aac）
- 返回 JSON 格式音乐列表（含歌曲名、音频路径、歌词路径）
- 带 CORS 跨域支持
- **结论**：需要独立 Linux 服务器运行，GitHub Pages 无法使用。如果用户有自己的服务器，可以保留源码备份；如果纯靠 GitHub Pages，此文件无实际作用

### jiangjun.mp4 恢复方案
- 文件实际 100.58MB，仅超 0.58MB
- 方案A：压缩视频（降低码率/分辨率，轻松压到 95MB 以下）→ 可直接提交
- 方案B：Git LFS → 需额外配置，GitHub 免费额度 1GB/月
- 方案C：上传B站/其他平台 → 页面用 iframe 嵌入，不占仓库空间
- 方案D：不恢复 → 移除 zhongcheng.html 中的视频标签

## 已完成

- [x] 读取 api/get_music_list.py 内容，分析功能和依赖
- [x] 检查 jiangjun.mp4 实际大小（100.58MB）
- [x] 创建 .gitignore
- [x] 创建 PROGRESS.md 进度文件
- [x] api/ 目录：**不恢复**（用户决策，GitHub Pages 无法运行 Python 后端）
- [x] jiangjun.mp4：**用户已自行压缩并放到根目录**（3.38MB，符合要求）
- [x] 修复断链1：egg.html 第255行 `./egg/male-photo3.jpg` → `./male-photo3.jpg`
- [x] 修复断链2：zhongcheng.html 视频引用 `./jiangjun.mp4` 路径正确，文件已就位（3.38MB）
- [x] 修复断链3：index.html 第685行 `videos.html` 无效跳转 → 改为 `alert('功能正在开发中，敬请期待！')`
- [x] 额外修复：zhongcheng.html 缺失的 `</audio>` 闭合标签已补全

## 验证结果

| 检查项 | 结果 |
|-------|------|
| egg.html 中无 `egg/male-photo3` 错误路径 | ✅ 通过 |
| index.html 中无 `videos.html` 引用 | ✅ 通过 |
| zhongcheng.html 引用 `./jiangjun.mp4` 且文件存在（3.38MB） | ✅ 通过 |
| zhongcheng.html `</audio>` 闭合标签存在 | ✅ 通过 |
| jiangjun.mp3（37.25MB）+ jiangjun.mp4（3.38MB）均在根目录 | ✅ 通过 |

## 待办

- [ ] 用户确认后提交 git（当前修改：egg.html、zhongcheng.html、index.html、新增 PROGRESS.md、.gitignore、jiangjun.mp4）
- [ ] 5个闲置文件是否清理（live2-cover.jpg、live3-cover.jpg、live4-cover.jpg、music-bg.jpg、logo1.png）— 待用户决定

## 关键约束

- GitHub Pages 是纯静态托管，**不支持** Python/PHP等后端
- GitHub 单文件大小限制 **100MB**（超过会被拒绝）
- 仓库总大小建议控制在 **1GB** 以内
- 日志文件、临时文件不应提交到 git
