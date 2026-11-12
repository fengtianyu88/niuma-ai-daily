# 交付与推送

工作区 `C:\Users\tiany\WorkBuddy\2026-11-12-10-41-56` 是脚本唯一真源，Skill 目录不复制脚本。

## 一、脚本

| 脚本 | 作用 |
|---|---|
| `make_cover.py` | 生成 900×383 封面（Pillow + Windows 中文字体） |
| `wechat_draft_push.py` | 抽取 HTML 复制区 → 上传封面 → `draft/add` / `draft/update` → 回读验证 |

### 封面

```bash
python make_cover.py --date 2026-11-12 --out cover_2026-11-12.jpg
```

- 主标题默认「牛马AI日报」，署名「算法牛马」，`--out` 默认 `cover_{date}.jpg`。
- 生成后用图片查看确认主标题无截断、无乱码、无遮挡。
- 封面是草稿**必填项**，且必须是永久图片素材，脚本会自动调 `material/add_material` 换 `media_id`。

### 推送

```bash
# 本地自检（不联网）
python wechat_draft_push.py --check

# 每日新建（当天已有同标题草稿则自动更新，不产生重复草稿）
python wechat_draft_push.py --upsert

# 同一期反复改稿：定位原草稿 media_id 后更新，可省略封面
python wechat_draft_push.py --update-media-id <media_id>
```

主要参数：`--html`（默认 `每日AI晨报_{date}_公众号.html`）、`--title`（默认 `牛马AI日报 · YYYY 年 M 月 D 日`）、`--author`（默认 `算法牛马`）、`--digest`（默认从速览提取）、`--cover`（默认 `cover_{date}.jpg`）、`--thumb-media-id`、`--update-media-id`、`--upsert`、`--check`。

日期全部按北京时间 `datetime.now(timezone(timedelta(hours=8))).date()` 动态生成，不写死。

## 二、凭据与安全

- 优先环境变量 `WECHAT_APPID` / `WECHAT_APPSECRET`（也可 `--appid` / `--secret`）。
- **无人值守回退**：环境变量未注入时，脚本从本工作区的历史会话记录（`C:\Users\tiany\.workbuddy\projects\c-Users-tiany-WorkBuddy-2026-11-12-10-41-56\*.jsonl`）检索 AppID 与 32 位 AppSecret，仅用于当次 API 调用。
- 铁律：**不回显、不打印、不写入任何文件、不写进日志或记忆**。绝不把 AppSecret 存成本地配置文件。
- AppID 不敏感；AppSecret 曾在对话里明文出现，长期自动运行前建议重置一次并改用环境变量注入。
- 公众号后台前置条件：已认证账号、IP 白名单（IPv4 优先，曾实测 `27.38.245.100`，可能变动）、草稿箱与素材管理权限。

### 国外网站抓取失败的 VPN 兜底（按流量计费，能直连就不用）

适用场景：WebFetch / WebSearch / curl 直连国外站点持续超时或被重置，且已排除是页面本身下线（换 URL、用搜索验证存在性）。**先直连，代理是最后手段。**

**订阅链接只存在本地一处**：工作区根目录 `vpn_subscription.txt`（用户明确要求落盘保存，每天日报要用）。**绝不把链接写进本 Skill 仓库或推到 GitHub**——仓库是公开的，写进去等于泄露。取回顺序：① 直接读该文件；② 文件丢失时从本工作区的历史会话记录（`~/.workbuddy/projects/` 下本工作区对应目录的 `*.jsonl`）按「包含 `subscribe?token=` 加 32 位十六进制串的订阅链接」特征检索。两种方式均仅当次使用、不回显完整 token、不复制到其他任何文件。

无头拉起流程（不依赖 GUI，不碰系统代理设置）。**以下三步都有实测踩坑，不可省**：

1. 下载订阅为 Clash YAML：`curl -s "<订阅链接>&flag=clash" -o %TEMP%\niuma_proxy.yaml`（临时目录，用完删）。**实测坑①：订阅站本身在 IPv4 下被 TCP 阻断（DNS 正常但连接超时），必须加 `-6` 走 IPv6**：`curl -6 -s --max-time 90 ...`。**实测坑②：慢链路下响应会中途截断（YAML 解析失败），订阅服务器不支持 Range 断点续传，只能加大 `--max-time`（如 300 秒）整份重拉**。**实测坑③：若订阅拉不下来，可直接用 Clash Verge 的缓存配置**：`%APPDATA%\io.github.clash-verge-rev.clash-verge-rev\profiles\` 下节点数不为 0 的 YAML（实判同一家订阅、mixed-port 7890，距更新久一点但节点可用）。
2. 启动内核：**必须带 `-d` 指到 Clash Verge 数据目录**（里面有现成的 `Country.mmdb` / geoip 数据库）：`"C:\Program Files\Clash Verge\verge-mihomo.exe" -d "%APPDATA%\io.github.clash-verge-rev.clash-verge-rev" -f <配置> > %TEMP%\niuma_mihomo.log 2>&1`（后台运行，默认混合端口 7890）。**实测坑：不带 `-d` 时内核找不到本地 MMDB 会去联网下载，被墙卡死、端口永不监听、日志只停在 "Can't find MMDB, start download"**。启动后等 3-5 秒再验证端口。
3. 仅给当次抓取命令注入代理：`curl -x http://127.0.0.1:7890 ...` 或 WebFetch 前设置 `HTTPS_PROXY=http://127.0.0.1:7890` 环境变量。**不开启系统代理**，避免全局流量走计费线路。返回 301/302 也算通（重定向），加 `-L` 跟随确认内容可达。
4. 抓取完成**立即**：杀掉 verge-mihomo 进程（`taskkill /IM verge-mihomo.exe /F`）+ 删除临时 YAML 与内核日志。
5. 流量纪律：只代理失败的域名请求，成功即关；禁止用代理跑大流量下载或批量抓取；忘记关闭视为事故。

核验关闭：`tasklist | grep -i mihomo` 无输出、`netstat` 无 7890 LISTEN、临时 YAML 已删，才算收尾（TIME_WAIT 属正常收尾状态，不算残留）。2026-11-12 实测：三个目标站 x.ai / mistral.ai / ai.meta.com 直连均 HTTP 000，过代理全部 200；订阅余量充足（约 900 GB 总量，已用约 3.6 GB）。

## 三、微信官方 API

基址 `https://api.weixin.qq.com/cgi-bin`。

| 步骤 | 端点 | 说明 |
|---|---|---|
| 取 token | `GET /token?grant_type=client_credential&appid=…&secret=…` | 每次运行重新取 |
| 上传封面 | `POST /material/add_material?access_token=…&type=image` | multipart，返回永久 `media_id` |
| 查当天草稿 | `POST /draft/batchget` | `{"offset":0,"count":20,"no_content":1}`，按标题匹配当天 |
| 新建草稿 | `POST /draft/add` | `{"articles":[article]}` |
| 更新草稿 | `POST /draft/update` | `{"media_id":…,"index":0,"articles":article}` |
| 回读总量 | `GET /draft/count` | 确认没有多出重复草稿 |
| 回读内容 | `POST /draft/get` | 比对标题、作者、封面、正文 |

article 字段：`title`、`author`、`digest`（≤120 字）、`content`、`content_source_url`、`thumb_media_id`、`need_open_comment`、`only_fans_can_comment`。

`draft/batchget` 取 `item[].content.news_item[0]`；`--upsert` 用当天标题匹配，命中则走 `draft/update` 并沿用原 `thumb_media_id`，未命中才 `draft/add`。

## 四、推送后必须回读验证

1. `draft/count`：草稿总数与推送前对比，改稿不应增加。
2. `draft/batchget` / `draft/get`：确认标题、作者、封面 `thumb_media_id` 存在、更新时间。
3. 比对**可见纯文本**：本地与远端各自去掉 HTML 标签与空白后逐字相等。
4. 比对链接数：`content.count('https://')` 本地 = 远端。
5. 禁止词残留检查（见 [layout.md](layout.md) 自检清单）在远端正文也跑一遍。

**注意**：远端 HTML 字符数通常比本地少几十到一百字符，是微信端删掉 HTML 注释、规范自闭合标签导致的，属正常。**不要拿字符数直接判等**，只比对可见纯文本与链接数。

## 五、错误码

| 码 | 处理 |
|---|---|
| 40001 | AppSecret 不正确或 token 失效，检查凭据 |
| 40013 | AppID 无效 |
| 40164 | 调用方 IP 不在白名单，更新公众号后台 IP 白名单（IPv4 优先） |
| 48001 | 接口未授权：多为未认证订阅号，或缺草稿箱 / 素材管理权限 |
| 40007 | `media_id` 无效，检查 `--thumb-media-id` |
| 45009 | 接口调用超过限额 |
| 53404 | 账号已被限制发布能力 |

已认证订阅号「算法牛马」实测 `draft/add` 与 `draft/update` 通道完全可用。

## 六、无人值守运行检查表

- [ ] 日期、标题、HTML 文件名、封面名都是当天，没有写死 `2026-11-12`
- [ ] 窗口为运行时刻往前 24 小时，全文只写窗口内
- [ ] 19 个来源官网 + arXiv 补漏都真查过，没查的不写进标题或结论
- [ ] 六部分齐全，速览六条，第二部分大标题「二、官方新报告」
- [ ] HTML 配平、每个 `<p>` 有 `text-align:left`、链接都在段末、禁止词零残留
- [ ] 封面主标题「牛马AI日报」，已上传为永久素材
- [ ] `--upsert` 保证当天重跑不产生重复草稿
- [ ] 回读验证通过，临时脚本已清理，本期结论已追加到工作区日志
