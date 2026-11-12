# 公众号 HTML 排版规范

参考央视新闻 / 机器之心的公众号版式：**全宽顶格、红色栏目条、紧凑行距、无左右大留白**。历史反馈是「左右两边留白太多」，不要再套居中窄容器。

## 一、文件骨架

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head><meta charset="utf-8"><meta name="viewport" content="width=device-width, initial-scale=1">
<title>牛马AI日报 · YYYY-MM-DD</title></head>
<body style="margin:0;padding:0;background:#ffffff;font-family:-apple-system,BlinkMacSystemFont,'PingFang SC','Microsoft YaHei',sans-serif;">

<!-- ===== 复制时选中下面这个 section 的全部内容，粘贴进公众号编辑器即可 ===== -->
<section id="wechat-article" style="margin:0;padding:0;background:#ffffff;color:#333333;font-size:16px;line-height:1.85;text-align:left;">
  …正文…
</section>
<!-- ===== 复制区结束 ===== -->

</body></html>
```

`wechat_draft_push.py` 只取两个注释之间的内容推送，注释本身不会进草稿。完整可复制骨架见 [../assets/article_skeleton.html](../assets/article_skeleton.html)。

## 二、不可违反的排版硬规矩

1. 主容器**禁用 `max-width` 与左右 `padding`**，禁用 `letter-spacing`，并显式写 `text-align:left`。
2. **每个 `<p>` 都必须带 `text-align:left`**。微信编辑器粘贴后默认套两端对齐，会把中英文混排的半角空格撑开成裂缝——这是「字中间空格比较大」的根因，不是 CSS 里写了 `justify`。
3. 大标题、眉标也禁用 `letter-spacing`；禁止用手工敲空格做眉标（「本 期 速 览」已改「本期速览」），禁止用全角空格 `\u3000` 做序号间隔，一律半角空格。
4. 正文块禁用大面积灰底卡片与内缩；**仅重点提示框**可留 `10px 12px` 小内边距 + 4px 左色条。
5. **来源链接位置：内容在前、链接在后**。每个条目的灰色链接段必须是该段**最后一个 `<p>`**，不许夹在标题与正文之间。链接段 `margin:8px 0 0`。
6. 微信正文外链不可跳转，来源用可复制的纯文本 `https://…`，多条用 `<br>` 换行，必要时加「论文 / 预印本 / 项目主页」等前缀，但前缀不写核查口径。
7. 时间统一北京时间；不写 UTC + 换算双写。

## 三、颜色与字号

| 用途 | 值 |
|---|---|
| 栏目红（3px 下边框 / 4px 章节竖条 / 眉标 / 左色条） | `#c0392b` |
| 正文 | `#333333`，16px，行高 1.85 |
| 小标题 / 大标题 | `#1f2d3d` 加粗 |
| 来源链接 | `#8a919b`，12px，`word-break:break-all` |
| 机构 / 日期行 | `#6b7280`，12px |
| 分隔线 | `1px dashed #e3e6eb`（条目之间）、`1px solid #e8eaed`（文末） |
| 重点提示框底色 | 绿 `#f0f7f4` / 蓝 `#eef4fc` / 橙 `#fff8f3` / 灰 `#fafbfc` |

## 四、可直接复用的样式串

```
主容器   margin:0;padding:0;background:#ffffff;color:#333333;font-size:16px;line-height:1.85;text-align:left;
头部     margin:0 0 18px;padding:0 0 16px;border-bottom:3px solid #c0392b;
大标题   margin:0 0 10px;font-size:27px;font-weight:800;color:#1f2d3d;line-height:1.3;letter-spacing:0;text-align:left;
日期     margin:0;font-size:15px;font-weight:700;color:#5b6470;text-align:left;
速览框   margin:0 0 8px;padding:10px 12px;background:#f7f8fa;border-left:3px solid #c0392b;border-radius:0;
眉标     margin:0 0 6px;font-size:12px;font-weight:700;color:#c0392b;letter-spacing:0;text-align:left;
速览条   margin:0 0 3px;font-size:14px;line-height:1.8;text-align:left;   （末条 margin:0;）
章节框   margin:30px 0 16px;padding:0;border-bottom:none;
章节标题 margin:0;padding:0 0 0 11px;border-left:4px solid #c0392b;font-size:18px;font-weight:700;color:#1f2d3d;line-height:1.45;text-align:left;
条目框   margin:0 0 18px;padding:0 0 14px;border-bottom:1px dashed #e3e6eb;
条目标题 margin:0 0 6px;font-size:16px;font-weight:700;color:#1f2d3d;text-align:left;
机构行   margin:0 0 10px;font-size:12px;color:#6b7280;text-align:left;
正文段   margin:0 0 8px;font-size:15px;text-align:left;   （末段 margin:0;）
链接段   margin:8px 0 0;font-size:12px;color:#8a919b;word-break:break-all;line-height:1.8;text-align:left;
提示框   margin:0 0 16px;padding:10px 12px;background:#eef4fc;border-left:4px solid #1a4d8f;border-radius:0;
署名框   margin:0;padding:14px 0 0;border-top:1px solid #e8eaed;
署名     margin:0;font-size:12px;color:#8a919b;line-height:1.8;text-align:left;
```

HTML 注释只写结构标记（如 `<!-- 第一部分 -->`），不写「供碎片时间扫读」「单一板块」这类写法说明。

## 五、结构顺序

1. 头部：红色 3px 下边框 + 大标题「牛马AI日报」+ 日期「YYYY 年 M 月 D 日 · 周X」。
2. 「本期速览」框：眉标只写「本期速览」，下面六条各一句结论，格式 `<strong>一 要闻</strong>｜…`。**不写「· 6 个信息点」**。
3. 六个章节：标题 + 内容。章节标题下**直接进内容**，不要盘点框、不要缺口说明、不要「今天有 N 条」。
4. 文末：「编发｜算法牛马」。

## 六、写完后自检

```python
import re
s = open('每日AI晨报_{date}_公众号.html', encoding='utf-8').read()
body = s.split('<!-- ===== 复制时选中下面这个 section 的全部内容，粘贴进公众号编辑器即可 ===== -->', 1)[1]
body = body.split('<!-- ===== 复制区结束 ===== -->', 1)[0]
assert body.count('<section') == body.count('</section>')
assert body.count('<p ') == body.count('</p>')
assert 'max-width' not in body and 'letter-spacing' not in body.replace('letter-spacing:0', '')
assert '\u3000' not in body
# 每个 p 都要左对齐
assert all('text-align:left' in st for st in re.findall(r'<p style="([^"]*)"', body))
# 每个含链接的 section，链接段必须是最后一个 p
for b in re.findall(r'<section[^>]*>(.*?)</section>', body, re.S):
    ps = re.findall(r'<p style="([^"]*)">(.*?)</p>', b, re.S)
    li = [j for j, (st, tx) in enumerate(ps) if 'https://' in tx]
    assert not li or li == list(range(len(ps) - len(li), len(ps)))
# 禁止词
for k in ['19 个来源', '13 家', '补上第 3 份', '其余 16 个', '不足 3 份',
          '6 个信息点', '没有新新闻稿', '集中批量更新', '（节选）',
          '牛马AI晨报', '牛马 AI', '检索窗口', '核查看板']:
    assert k not in body, k
```

再核对：六部分标题齐全、速览六条、`二、官方新报告` 不带来源数量、30 条左右来源链接、所有链接都是纯文本而非 `<a href>`。

## 七、本地渲染预览

用 Edge / Chrome headless：

- **不要**直接用 390px 视口 + `--force-device-scale-factor=2`，会出现横向溢出误判。
- 生成临时预览 HTML：800px 视口，外层 `background:#e9edf2`，内容栏 `width:375px;margin:0 auto;padding:0 16px;background:#fff`（外层仅 16px 平台边距）。
- 在六个章节注释前插 1–2px 洋红定位线，`--window-size=800,20000` 截长图，按洋红行聚簇定位章节，裁前用上一行像素抹掉标线。
- 裁四张 407px 宽图：`排版预览_顶部特写.png`（0 → 第一部分锚点）、`中段特写.png`（第二部分锚点 → 约 +1500px）、`第六部分.png`（第六部分锚点 → 内容底部）、`手机端.png`（全图裁到内容底部）。
- headless 截图写入有延迟，先轮询确认文件存在且大小稳定再裁剪；过早检查会误报 FAIL。
