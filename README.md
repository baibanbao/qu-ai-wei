# 去 AI 味 · qu-ai-wei

一个 Claude skill，清理中文稿子里的 AI 痕迹。合并三家规则，并把它们互相矛盾的地方一条条裁掉。

## 为什么又做一个

去 AI 味的规则集已经有几份，问题是它们互相打架。

一家说「AI 爱把段落写得一样长，真文章有毛边」；另一家拿 300 篇 AI 样本、117.9 万汉字、5 个模型实测，句长段长的离散度人机无差别。照前者改，你会为了制造参差去乱调句长；照后者改，你又会放过那些确实该删的东西。

类似的顶牛有七处：排比、被动句、破折号、抽象换具体、段首评论语、句首「其实」、毛边。这个 skill 做的事是把三家合到一起，冲突处给出裁决和依据，再按「稿子是谁的」分成两种模式。

## 三家来源

| 来源 | 管什么 | 取了什么 |
|------|--------|----------|
| 王佩写作系统 v4.0 | 像不像人写的 | 病根三条、语言禁区、声音校准、协作档位 |
| [humanizer-zh](https://github.com/op7418/Humanizer-zh) | 删的时候别删坏 | 信息守恒、保留边界、交付前核对 |
| [lieflat-less-ai-tone](https://github.com/larashero3-dotcom/lieflat-less-ai-tone) | 哪些是真痕迹 | 十一条实测规则、「不作为改写理由」表 |

第一家是作者自用的写作系统，靠经验。另两家是开源项目，MIT。lieflat 靠语料实测——本 skill 里所有带数字的频率（每千字、倍率、覆盖率）都出自它。

## 两个模式

先选模式，这是整个 skill 的分水岭。

**自家稿**——你自己的文章。可以下狠刀，可以标缺口向作者要料，可以按作者的声音校准。默认输出红笔报告，不动原稿。

**白名单**——别人的稿、译稿、成稿清理，或者你只想去味、不想被改写法。只动清单内明确命中的条目，其余逐字保留，直接交改写全文。判不准是否命中，保持原文。

不分模式，两套逻辑会互相踩：白名单式改写要求「原文抽象就让它抽象」，而生成式写作系统的核心动作恰恰是「抽象换物象」。

## 七处裁决

| 冲突 | 裁决 |
|------|------|
| 毛边 | 离散度实测无差别。毛边是写出来的结果，不是改出来的手段。不为制造参差调句长或拆段，也不把原本参差的改齐 |
| 排比 | 句内同构排比人类用得不比 AI 少，留。相邻句复用同一句法骨架，改 |
| 被动句 | 被动本身不改。只有一种情况改：文章要追责，而被动句正把责任人藏起来 |
| 抽象换物象 | 看模式。自家稿标缺口向作者要料；白名单只把原文已有的具体值提到概括词的位置 |
| 破折号 | 实测 Claude 是人类的 5.3 倍，该删。但作者本人爱用时，以作者习惯为准 |
| 段首「值得注意的是」 | 白名单补一个「这」字；自家稿整句删 |
| 句首「其实」 | 独立成分的删，嵌在句中承担转折的留 |

依据都写在 SKILL.md 第八节。

## 硬边界

不管哪个模式，三条优先于所有去味规则。

1. **信息守恒**。不新增原文没有的事实、数字、名字、日期、引语、出处、因果、动机、场景。改写后每个实词都要能在原文指出出处。反方向同样禁止：不删观点、限定和让步。把「可能提升」改成「提升」是篡改语气强度，不是去味。
2. **最小改动**。命中规则的句子也只改解决该问题所必需的部分。不顺便润色。
3. **结构不动**。标题层级、章节顺序、段落数量与顺序、列表、表格、引用、代码块的位置，全部保留。

一句话：宁可留一点 AI 味，不可造一个假事实。

## 安装

```bash
git clone https://github.com/baibanbao/qu-ai-wei.git ~/.claude/skills/qu-ai-wei
```

项目级安装放 `.claude/skills/qu-ai-wei/`。Claude Desktop 和 claude.ai 直接上传 `SKILL.md` 也可以。

## 用法

对 Claude 说「去AI味」或「去味」，附上稿子或文件路径。第一次会问你用哪个模式。

自家稿模式输出 `B级编辑-<原稿名>.md`，行号定位，每处给两三个改写方向，你拍板。白名单模式直接交改写全文，不解释改了什么，你问时再说。

## 目录

SKILL.md 十三节：

硬边界 · 病根三条 · 三条门槛（全角标点／零粗体／零惊叹号）· 十一条实测规则 · 老编辑的刀（按开头／段间／结尾／句内排）· 黑名单 · 不许动 · 七处裁决 · 加回来 · 分文体加戏 24 项 · 声音校准 · 报告格式 · 交付前核对

其中「不许动」和「不作为改写理由」两张表与改写清单同等重要。去味最容易变成把稿子刮平。

## 边界

- 不判断作者身份。频率差异是频率差异，不是来源证据。
- 不保证通过任何 AI 检测器。
- 只管去味，不管怎么写。结构、立意、叙事技法不在范围内。

## English

A Chinese-writing skill for Claude that strips AI tells from finished drafts.

It merges three rule sets — the author's own writing system, plus the open-source [humanizer-zh](https://github.com/op7418/Humanizer-zh) and [lieflat-less-ai-tone](https://github.com/larashero3-dotcom/lieflat-less-ai-tone) — and adjudicates the seven places where they contradict each other, favoring corpus-measured evidence over intuition. Every frequency figure in the skill comes from lieflat's corpus study (300 AI samples, 1.179M characters, 5 models).

Two modes. **Own draft**: a full editorial pass, delivered as a red-pen report with line numbers and two or three rewrite options per hit. **Whitelist**: only listed patterns are touched; every unmatched sentence is preserved verbatim.

One rule overrides everything: never invent a fact, a number, a name, or a quote to make a sentence sound more human. Losing a hedge is tampering, not humanizing. The skill makes no claim about authorship detection and does not promise to defeat any AI detector.

Documentation is in Chinese. MIT.

## 许可与致谢

MIT。

`humanizer-zh`（MIT，歸藏）与 `lieflat-less-ai-tone`（MIT，shiujan）的规则与实测数据构成本 skill 的两块基石，致谢见 SKILL.md 文末。数据不是我测的，规则是我裁的。
