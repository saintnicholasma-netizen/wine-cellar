# 🍷 Wine Cellar Project — Claude.md 项目日志

> **Hard Rule**: 未来的每一次版本更新都必须记录在此文档中，包括更新内容、日期、遇到的问题和解决方案。

---

## 项目概述

一个基于 React 18 的单页面酒窖管理网站，用于管理 Nick 和其父母的葡萄酒收藏。部署在 Cloudflare Pages，通过 GitHub 自动部署。

- **技术栈**: React 18 (UMD, 纯 `React.createElement()`), 单一 `index.html` 文件
- **部署**: Cloudflare Pages → `wine-cellar-cme.pages.dev`
- **GitHub**: `saintnicholasma-netizen/wine-cellar` (main 分支)
- **数据持久化**: localStorage + 内置默认常量 (NICK_WINES, PARENT_WINES)

---

## 完整版本历史

### v1.0 — 初始版本
- 创建基础酒窖网站
- Nick 的个人藏酒列表 (NICK_WINES)
- 基础卡片式 UI，支持展开查看详情
- 三个状态标签页：已拥有 (owned) / 推荐 (recommended) / 已喝 (drank)
- 国旗 emoji 映射 (COUNTRY_FLAGS)
- 基础排序功能（年份、国家、酒名、窗口、评分）
- 深色/浅色模式切换
- localStorage 数据持久化

### v2.0 — 父母酒窖 (commit `be7dfc5`)
- **新增**: 父母酒窖标签页，包含 45 款酒
- **数据来源**: 从 `酒窖档案_Wine_Cellar.xlsx` 通过 Python 脚本 (`gen_parent_wines.py`) 生成 `PARENT_WINES` 数组
- **数据字段**: id, name, nameEn, region, regionEn, country, grape, grapeEn, vintage, price, status, window, notes, pairing, awards
- **特别处理**: Sassicaia 2016 (p30) 有 qty:2（两瓶）
- **覆盖国家**: 中国、法国、意大利、南非、西班牙、新西兰、澳大利亚、智利、阿根廷、斯洛文尼亚

### v2.1 — 价格排序与人民币转换 (commit `81efb59`)
- **新增排序**: 价格↑/价格↓ 排序选项
- **新增**: `parsePrice()` 函数解析多种价格格式（£15-20, $489, ¥98-150, £424-495/瓶）
- **新增**: ¥ 人民币转换按钮，切换显示所有价格的人民币等值
- **汇率**: £1≈¥9.2, $1≈¥7.3, €1≈¥7.9 (CURRENCY_RATES)
- **新增**: `priceToRMB()` 处理范围价格、单价、后缀（/瓶, +, (估)）
- **新增**: 默认排序重置按钮（红色 ↻ 默认排序）

### v2.2 — 饮用窗口研究更新 (commit `7ab8392`)
- **扩展窗口分类**: 从简单的 now/cellar 扩展为精细分类
  - now (现在可喝 🟢)
  - soon (1-3年 🔵)
  - mid (3-5年 🟡)
  - wait (3-5年等 🟡)
  - long (5-10年 🔴)
  - cellar (长期陈年 🟣)
  - vlong (10年+ 🟣)
  - unknown (未知 ⚪)
- **研究**: 对 26+ 款酒进行了网络研究，根据实际陈年潜力更新窗口
- **关键原则**: 便宜酒（约¥100）不应标记为长期陈年

### v2.3 — 数量追踪与购买按钮 (commit `7c5e21b`)
- **新增**: 瓶数追踪功能 (qty 字段)
- **数量调节器**: 展开卡片时显示 [−] 数量 [+] 调节器
- **智能喝酒流程**: qty > 1 时，点击"喝了"会减少数量并创建新的已喝记录（不会删除原酒条目）
- **数量徽章**: 瓶子图标上显示蓝色 ×N 徽章
- **新增**: "🛒 已买入手" 按钮，推荐酒一键转为已拥有
- **统计更新**: 已有瓶数统计改为 qty 总和而非酒款数

### v3.0 — 陈年曲线与品饮指南 (commit `429370f` + 后续)
- **新增**: 陈年曲线可视化 (`AgingCurve` 组件)
  - 基于葡萄品种、产区、价格档次生成个性化陈年曲线
  - SVG 渲染：巅峰期高亮、"现在"标记、年份x轴
  - 底部信息：巅峰年份范围、瓶龄、建议保存至
  - 阶段标签：🌱成长期 / ⭐巅峰期 / 🍎成熟期 / 🍂衰退期
  - `getAgingProfile()` 支持 20+ 葡萄品种的陈年特性
- **新增**: 品饮指南 (`DrinkGuide` 弹窗)
  - 点击绿色"建议品饮"统计卡或"现在推荐品饮"栏打开
  - 每款建议品饮酒显示：为什么现在喝、适合场合、推荐餐厅类型、配餐建议、供酒温度、醒酒时间
  - `getDrinkGuide()` 基于酒类型（红/白/甜/气泡/波特/干邑）和价格档次生成建议
- **新增**: claude.md 项目文档

### v3.1 — 国家探索与品饮记录 (commit `fda3903`)
- **新增**: 国家探索弹窗 (`CountryExplorer`)，点击覆盖国家卡片浏览各国藏酒
- **新增**: 品饮记录弹窗 (`DrankHistory`)，按评分从高到低查看品饮历史
- **移除**: "现在推荐品饮"横条，功能已整合进品饮指南弹窗

### v3.2 — 采购推荐弹窗 (commit `94a90cc`)
- **新增**: 采购推荐弹窗 (`RecommendationGuide`)，点击橙色采购推荐卡片查看推荐酒详情和购买理由
- **新增**: `getRecommendReason()` 基于奖项、价格、产区、品种生成购买理由
- **变更**: 推荐酒从主列表移除（`nonRec` 过滤），整合到采购推荐弹窗中
- **变更**: 移除"推荐"筛选标签

### v3.3 — 父母采购推荐数据 (commit `538e735`)
- **数据来源**: 从 `父母选酒推荐_Wine_Recommendations.xlsx` 提取 18 款推荐酒
  - 送礼宴请级 (¥300-800): 10 款 (p46-p55)，包括 Rioja Alta 904、Barolo、Pauillac、Brunello、Chablis GC 等
  - 特殊场合收藏级 (¥800+): 8 款 (p56-p63)，标记 priority:"high"，包括 Hermitage、Krug、Dom Pérignon、Margaux 副牌等
- **新字段**: `recommendReason` — Excel 中手写的推荐理由，优先于自动生成
- **增强**: `getRecommendReason()` 新增 15+ 产区/品种识别（Hermitage、Côte-Rôtie、Champagne、Gevrey-Chambertin、Puligny-Montrachet、Meursault、Margaux、Pauillac、Brunello、Rioja、Chablis、Alsace Riesling）
- **覆盖国家新增**: 葡萄牙（爸妈酒窖从10国增至11国）

### 开发规范：添加新酒时的自动更新
添加新酒时，需确保以下内容自动匹配：
1. `getAgingProfile()` — 如果新葡萄品种不在已有列表中，需添加对应陈年档案
2. `getDrinkGuide()` — 如果新酒类型不在已有分类中，需添加品饮指南逻辑
3. 品饮推荐条件基于 `getRec()` 函数返回的状态，确保新酒的 window 设置正确

---

## 用户偏好

1. **语言**: 界面以中文为主，酒名中英文双语
2. **单文件架构**: 所有代码在一个 index.html 中，不使用 JSX/Babel
3. **实用主义**: 便宜酒不应标记长期陈年，数据要基于实际研究
4. **交互简洁**: 一键操作（购买、喝酒、排序重置）
5. **数据安全**: 提供重置初始数据按钮
6. **深色模式**: 支持深色/浅色切换
7. **功能需先问清楚**: 用户偏好在开始前先确认需求细节

---

## 经验教训

### ✅ 做对了的事情
- 用 Python pandas 从 Excel 提取数据，生成干净的 JS 数组
- 对饮用窗口进行实际研究而非凭空猜测
- qty-aware 的喝酒流程设计（分裂而非覆盖）
- 价格排序中 parsePrice() 对多种格式的兼容处理
- 人民币转换对范围价格的正确处理

### ❌ 犯过的错误
- **File modified since read**: 用 Python 修改文件后直接尝试 Edit，忘记重新 Read 文件
- **窗口类别不匹配**: 生成的父母酒数据使用了 WINDOW_META 中不存在的类别（soon, wait, cellar），应该在生成数据时就确保类别匹配，或同时更新两处
- **点击元素失败**: 使用 ref_id 点击标签页时多次失败，最终通过坐标点击解决
- **Zoom 截图空白**: 部分 zoom 截图返回空白，需改用全屏截图

### 🚫 不应该做的事情
- 不要在修改文件后忘记重新 Read
- 不要假设 ref_id 点击总是可靠的，必要时用坐标
- 不要凭空编造饮用窗口数据，必须基于研究
- 不要把便宜酒标记为长期陈年
- 不要在没有确认类别映射一致的情况下生成数据

---

## 当前数据模型

```javascript
{
  id: "n1" | "p1",           // n=Nick, p=Parents
  name: "中文名",
  nameEn: "English Name",
  region: "产区（中文）",
  regionEn: "Region English",
  country: "国家",
  grape: "葡萄品种",
  grapeEn: "Grape English",
  vintage: 2016 | null,
  price: "£424-495/瓶",
  status: "owned" | "recommended" | "drank",
  window: "now|soon|mid|wait|long|cellar|vlong|unknown",
  notes: "品鉴笔记",
  pairing: "配餐建议",
  awards: "评分与奖项",
  rating: 1-5 | null,        // 用户评分（喝后）
  drankDate: "2026-03-15",   // 喝酒日期
  priority: "high|medium|low",
  qty: 1,                    // 瓶数
  recommendReason: "手写推荐理由" // 可选，来自Excel的自定义推荐理由
}
```

---

## 待开发功能

- [x] 陈年曲线可视化：点击酒卡展开时显示该酒的成熟度/陈年曲线 ✅ v3.0
- [x] 品饮指南：点击建议品饮卡打开详细品饮场合/配餐/餐厅建议 ✅ v3.0
- [x] 国家探索弹窗：点击覆盖国家卡片，浏览各国藏酒 ✅ v3.1
- [x] 品饮记录弹窗：点击已品饮卡片，按评分从高到低查看品饮历史 ✅ v3.1
- [x] 移除"现在推荐品饮"横条（功能已整合进品饮指南弹窗） ✅ v3.1
- [x] 采购推荐弹窗：点击采购推荐卡片，查看推荐酒详情和购买理由 ✅ v3.2
- [x] 推荐酒从主列表移除，整合到采购推荐弹窗中 ✅ v3.2
- [ ] （未来功能待用户提出）

---

*最后更新: 2026-03-15*
