# 19 个监控源与检索口径

## 一、名单（一套，不分新老）

**一共 19 个来源 = 原 13 家 + 并入 6 家**。19 个全部每日必查，全部走同一套「先各自官网、后 arXiv 补漏」流程。不存在「只监控这 6 家」，也不存在「arXiv 补漏只给新增源用、老 13 家只以官网为准」。

- **原 13 家**：国外 6 = Anthropic、OpenAI、Google DeepMind、xAI、Meta、Mistral AI；国内 7 = DeepSeek、通义千问、Kimi、智谱清言、豆包、文心、混元。
- **并入 6 家**：NVIDIA Technical Blog、MiniMax、阶跃星辰、上海 AI Lab、华为诺亚方舟实验室、面壁智能。

国内 7 家在清单里写的是产品名，实际核查对象是背后的团队（Qwen 团队、字节 Seed、百度 ERNIE、腾讯 Hunyuan 等），别只盯产品名漏掉同公司其他研究线。

## 二、官网核查顺序

1. 逐个查 19 个来源官方渠道在**过去 24 小时（北京时间）**内新发布的技术报告 / 研究文章 / 论文公告，核对一手链接与发布日期。
2. 再查 arXiv 上 19 个来源同窗口的新预印本补漏。
3. 去重：同一成果官网与 arXiv 都出现只计一次，以官网为主。
4. 判定：模型 / 产品发布、方案介绍博客不算技术报告；无发布日期、无法确认落在窗口内的条目不收；只有代码提交不收。
5. 正文只写窗口内，挑最主要 3 份深讲；不足 3 份按实际数量输出，缺口结论只进 Markdown 底稿。

**没真查的来源不许写进标题或结论。**

## 三、一手官网入口

### 已实际访问验证（并入 6 家）

| 来源 | 一手入口 | 备注 |
|---|---|---|
| NVIDIA Technical Blog | `https://developer.nvidia.com/blog/` （中文镜像 `https://developer.nvidia.cn/blog/`） | 技术文章带发布日期，更新频繁 |
| MiniMax | `https://www.minimax.io/research` （正文在 `/blog/…`） | 研究列表带日期；H3-Image 属模型发布公告，不计入技术报告 |
| 阶跃星辰 StepFun | `https://www.stepfun.com/` ；研究页 `https://chat.stepfun.com/research` ；发布页 `/news/…` | 官网 Model Blog 与 Research 两条线 |
| 上海 AI Lab | `https://www.shlab.org.cn/` ；科研动态 `https://www.shlab.org.cn/info` ；研究 `https://www.shlab.org.cn/research` | 新闻条目带日期 |
| 华为诺亚方舟实验室 | `https://www.noahlab.com.hk/en` | Latest News / Selected Papers，论文条目常直链 arXiv abs 页 |
| 面壁智能 OpenBMB | `https://www.openbmb.cn` ；动态 `https://openbmb.cn/news` | 模型与技术报告常同步发在 GitHub `OpenBMB` 组织 |

### 原 13 家

不硬编码域名。**每期用机构名 +「research / blog / news / publications」检索其官方研究页，实际访问确认域名与日期后再写入底稿**；未验证的域名不写进正文。常见形态：机构主站下的 `/research`、`/blog`、`/news`，以及官方 GitHub 组织的 Releases / 技术报告仓库。

工作区 `每日AI晨报_监控源清单.md` 留有本期的核查记录可作起点，但每次仍须现查，不能拿旧记录当今日事实。

## 四、arXiv 补漏

### 关键限制

**arXiv 元数据没有机构（affiliation）字段，也没有按单位过滤的官方语法。** 只能用「机构别名 + 代表性模型 / 项目名」做全文检索，再人工核对作者署名。存在漏检与误命中，**不得宣称「已完整覆盖」**。

- 端点：`https://export.arxiv.org/api/query`，返回 Atom XML。
- `search_query` 字段前缀：`ti / au / abs / cat / co / jr / rn / id / all`；可配 `submittedDate:[YYYYMMDDHHMM TO YYYYMMDDHHMM]`（**GMT/UTC**）与 `sortBy=submittedDate&sortOrder=descending`。
- 北京时间窗口换算成 UTC 再写 `submittedDate`；例如 06:15 北京 = 前一日 22:15 UTC。
- 请求间隔不少于 3 秒；分组查询，避免单条 URL 过长。
- 命中项须核对标题、submitted 日期、摘要里的机构线索；核对不上不收，**不为凑数放宽**。

### 19 源别名表（检索关键词，不是链接）

| 来源 | 关键词 |
|---|---|
| Anthropic | Anthropic、Anthropic Research、Claude |
| OpenAI | OpenAI、GPT、ChatGPT |
| Google DeepMind | Google DeepMind、DeepMind、Gemini、Gemma |
| xAI | xAI、Grok |
| Meta | Meta AI、FAIR、Llama |
| Mistral AI | Mistral AI、Mistral、Mixtral |
| DeepSeek | DeepSeek、DeepSeek-AI |
| 通义千问 | Qwen、Tongyi、Alibaba |
| Kimi | Kimi、Moonshot AI |
| 智谱清言 | Zhipu、Z.ai、GLM、ChatGLM |
| 豆包 | Doubao、ByteDance Seed、Seed |
| 文心 | ERNIE、Baidu |
| 混元 | Hunyuan、Tencent |
| NVIDIA | NVIDIA、NVIDIA Research、Nemotron |
| MiniMax | MiniMax、MiniMax AI、MiniMax-M2 / M3 / H3 |
| 阶跃星辰 | StepFun、Step-3、Step-Audio、Step 5 |
| 上海 AI Lab | Shanghai AI Laboratory、Shanghai Artificial Intelligence Laboratory、InternLM、Intern、OpenGVLab、书生 |
| 华为诺亚 | Huawei Noah's Ark Lab、Noah's Ark Lab、Huawei Technologies |
| 面壁智能 | ModelBest、OpenBMB、MiniCPM、VoxCPM |

### 分组建法与误命中

按 4 组左右拆分，例如：
1. `all:"Anthropic" OR all:"DeepMind" OR all:"DeepSeek" OR all:"Qwen"`
2. `all:"MiniMax" OR all:"StepFun" OR all:"Shanghai AI Laboratory"`
3. `all:"NVIDIA" OR all:"Noah's Ark Lab" OR all:"OpenBMB"`
4. 当期重点项目名（如 `Taiyi-Bench`、`Sage-Taiyi`）单独补一组

**通用词误命中警告**：GPT、Llama、Gemini、GLM、Seed、Intern 以及 `minimax regret` 这类数学术语、GPU 型号，会被大量第三方论文命中。必须与机构名或更专的项目名组合并核对署名。精确短语命中率很低（实测 `all:"Shanghai AI Laboratory"` 只返回 1 条），别依赖单一关键词。

## 五、底稿记录格式

底稿里建议保留（公众号正文不写）：

- 检索窗口的北京时间起止，以及换算后的 UTC `submittedDate`。
- 19 源官网逐家核对结果：命中 / 窗口内无 / 不计入（附理由）。
- arXiv 分组检索式、命中数、窗口内新提交数、误命中归类。
- 同一成果官网与 arXiv 重复时的取舍说明。

这些是复现与审计的依据，不是给读者看的内容。
