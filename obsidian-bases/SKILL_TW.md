---
name: obsidian-bases
description: 建立和編輯包含檢視、過濾器、公式和摘要的 Obsidian Bases（.base 檔案）。在處理 .base 檔案、建立筆記的資料庫式檢視，或當使用者提及 Obsidian 中的 Bases、表格檢視、卡片檢視、過濾器或公式時使用。
---

# Obsidian Bases 技能

此技能讓相容的代理程式能夠建立和編輯有效的 Obsidian Bases（`.base` 檔案），包括檢視、過濾器、公式和所有相關配置。

## 概述

Obsidian Bases 是基於 YAML 的檔案，用於定義 Obsidian 儲存庫中筆記的動態檢視。Base 檔案可以包含多個檢視、全域過濾器、公式、屬性配置和自訂摘要。

## 檔案格式

Base 檔案使用 `.base` 副檔名並包含有效的 YAML。它們也可以嵌入在 Markdown 程式碼區塊中。

## 完整架構

```yaml
# 全域過濾器應用於 base 中的所有檢視
filters:
  # 可以是單一過濾器字串
  # 或是包含 and/or/not 的遞迴過濾器物件
  and: []
  or: []
  not: []

# 定義可在所有檢視中使用的公式屬性
formulas:
  formula_name: 'expression'

# 配置屬性的顯示名稱和設定
properties:
  property_name:
    displayName: "Display Name"
  formula.formula_name:
    displayName: "Formula Display Name"
  file.ext:
    displayName: "Extension"

# 定義自訂摘要公式
summaries:
  custom_summary_name: 'values.mean().round(3)'

# 定義一個或多個檢視
views:
  - type: table | cards | list | map
    name: "View Name"
    limit: 10                    # 可選：限制結果
    groupBy:                     # 可選：分組結果
      property: property_name
      direction: ASC | DESC
    filters:                     # 檢視特定過濾器
      and: []
    order:                       # 按順序顯示的屬性
      - file.name
      - property_name
      - formula.formula_name
    summaries:                   # 將屬性映射到摘要公式
      property_name: Average
```

## 過濾器語法

過濾器縮小結果範圍。它們可以全域應用或按檢視應用。

### 過濾器結構

```yaml
# 單一過濾器
filters: 'status == "done"'

# AND - 所有條件必須為真
filters:
  and:
    - 'status == "done"'
    - 'priority > 3'

# OR - 任何條件可以為真
filters:
  or:
    - 'file.hasTag("book")'
    - 'file.hasTag("article")'

# NOT - 排除匹配的項目
filters:
  not:
    - 'file.hasTag("archived")'

# 巢狀過濾器
filters:
  or:
    - file.hasTag("tag")
    - and:
        - file.hasTag("book")
        - file.hasLink("Textbook")
    - not:
        - file.hasTag("book")
        - file.inFolder("Required Reading")
```

### 過濾器運算子

| 運算子 | 描述 |
|----------|-------------|
| `==` | 等於 |
| `!=` | 不等於 |
| `>` | 大於 |
| `<` | 小於 |
| `>=` | 大於或等於 |
| `<=` | 小於或等於 |
| `&&` | 邏輯且 |
| `\|\|` | 邏輯或 |
| <code>!</code> | 邏輯非 |

## 屬性

### 三種屬性類型

1. **筆記屬性** - 來自前置元數據：`note.author` 或僅 `author`
2. **檔案屬性** - 檔案元數據：`file.name`、`file.mtime` 等
3. **公式屬性** - 計算值：`formula.my_formula`

### 檔案屬性參考

| 屬性 | 類型 | 描述 |
|----------|------|-------------|
| `file.name` | 字串 | 檔案名稱 |
| `file.basename` | 字串 | 不含副檔名的檔案名稱 |
| `file.path` | 字串 | 檔案的完整路徑 |
| `file.folder` | 字串 | 父資料夾路徑 |
| `file.ext` | 字串 | 檔案副檔名 |
| `file.size` | 數字 | 檔案大小（位元組） |
| `file.ctime` | 日期 | 建立時間 |
| `file.mtime` | 日期 | 修改時間 |
| `file.tags` | 列表 | 檔案中的所有標籤 |
| `file.links` | 列表 | 檔案中的內部連結 |
| `file.backlinks` | 列表 | 連結到此檔案的檔案 |
| `file.embeds` | 列表 | 筆記中的嵌入 |
| `file.properties` | 物件 | 所有前置元數據屬性 |

### `this` 關鍵字

- 在主要內容區域：指向 base 檔案本身
- 當嵌入時：指向嵌入檔案
- 在側邊欄：指向主要內容中的活動檔案

## 公式語法

公式從屬性計算值。在 `formulas` 部分定義。

```yaml
formulas:
  # 簡單算術
  total: "price * quantity"
  
  # 條件邏輯
  status_icon: 'if(done, "✅", "⏳")'
  
  # 字串格式化
  formatted_price: 'if(price, price.toFixed(2) + " dollars")'
  
  # 日期格式化
  created: 'file.ctime.format("YYYY-MM-DD")'
  
  # 複雜表達式
  days_old: '((now() - file.ctime) / 86400000).round(0)'
```

## 函式參考

### 全域函式

| 函式 | 簽名 | 描述 |
|----------|-----------|-------------|
| `date()` | `date(string): date` | 將字串解析為日期。格式：`YYYY-MM-DD HH:mm:ss` |
| `duration()` | `duration(string): duration` | 解析持續時間字串 |
| `now()` | `now(): date` | 目前日期和時間 |
| `today()` | `today(): date` | 目前日期（時間 = 00:00:00） |
| `if()` | `if(condition, trueResult, falseResult?)` | 條件式 |
| `min()` | `min(n1, n2, ...): number` | 最小數字 |
| `max()` | `max(n1, n2, ...): number` | 最大數字 |
| `number()` | `number(any): number` | 轉換為數字 |
| `link()` | `link(path, display?): Link` | 建立連結 |
| `list()` | `list(element): List` | 如果不是列表則包裝成列表 |
| `file()` | `file(path): file` | 取得檔案物件 |
| `image()` | `image(path): image` | 建立用於渲染的圖片 |
| `icon()` | `icon(name): icon` | 按名稱取得 Lucide 圖示 |
| `html()` | `html(string): html` | 渲染為 HTML |
| `escapeHTML()` | `escapeHTML(string): string` | 跳脫 HTML 字元 |

### 任何類型函式

| 函式 | 簽名 | 描述 |
|----------|-----------|-------------|
| `isTruthy()` | `any.isTruthy(): boolean` | 強制轉換為布林值 |
| `isType()` | `any.isType(type): boolean` | 檢查類型 |
| `toString()` | `any.toString(): string` | 轉換為字串 |

### 日期函式與欄位

**欄位：** `date.year`、`date.month`、`date.day`、`date.hour`、`date.minute`、`date.second`、`date.millisecond`

| 函式 | 簽名 | 描述 |
|----------|-----------|-------------|
| `date()` | `date.date(): date` | 移除時間部分 |
| `format()` | `date.format(string): string` | 使用 Moment.js 模式格式化 |
| `time()` | `date.time(): string` | 取得時間字串 |
| `relative()` | `date.relative(): string` | 人類可讀的相對時間 |
| `isEmpty()` | `date.isEmpty(): boolean` | 日期永遠為 false |

### 日期算術

```yaml
# 持續時間單位：y/year/years, M/month/months, d/day/days, 
#                 w/week/weeks, h/hour/hours, m/minute/minutes, s/second/seconds

# 加/減持續時間
"date + \"1M\""           # 加 1 個月
"date - \"2h\""           # 減 2 小時
"now() + \"1 day\""       # 明天
"today() + \"7d\""        # 從今天起一週

# 減日期以取得毫秒差異
"now() - file.ctime"

# 複雜持續時間算術
"now() + (duration('1d') * 2)"
```

### 字串函式

**欄位：** `string.length`

| 函式 | 簽名 | 描述 |
|----------|-----------|-------------|
| `contains()` | `string.contains(value): boolean` | 檢查子字串 |
| `containsAll()` | `string.containsAll(...values): boolean` | 所有子字串都存在 |
| `containsAny()` | `string.containsAny(...values): boolean` | 任何子字串存在 |
| `startsWith()` | `string.startsWith(query): boolean` | 以查詢開頭 |
| `endsWith()` | `string.endsWith(query): boolean` | 以查詢結尾 |
| `isEmpty()` | `string.isEmpty(): boolean` | 空值或不存在 |
| `lower()` | `string.lower(): string` | 轉小寫 |
| `title()` | `string.title(): string` | 轉標題大小寫 |
| `trim()` | `string.trim(): string` | 移除空白字元 |
| `replace()` | `string.replace(pattern, replacement): string` | 取代模式 |
| `repeat()` | `string.repeat(count): string` | 重複字串 |
| `reverse()` | `string.reverse(): string` | 反轉字串 |
| `slice()` | `string.slice(start, end?): string` | 子字串 |
| `split()` | `string.split(separator, n?): list` | 分割為列表 |

### 數字函式

| 函式 | 簽名 | 描述 |
|----------|-----------|-------------|
| `abs()` | `number.abs(): number` | 絕對值 |
| `ceil()` | `number.ceil(): number` | 無條件進位 |
| `floor()` | `number.floor(): number` | 無條件捨去 |
| `round()` | `number.round(digits?): number` | 四捨五入到指定位數 |
| `toFixed()` | `number.toFixed(precision): string` | 定點數表示法 |
| `isEmpty()` | `number.isEmpty(): boolean` | 不存在 |

### 列表函式

**欄位：** `list.length`

| 函式 | 簽名 | 描述 |
|----------|-----------|-------------|
| `contains()` | `list.contains(value): boolean` | 元素存在 |
| `containsAll()` | `list.containsAll(...values): boolean` | 所有元素都存在 |
| `containsAny()` | `list.containsAny(...values): boolean` | 任何元素存在 |
| `filter()` | `list.filter(expression): list` | 按條件過濾（使用 `value`、`index`） |
| `map()` | `list.map(expression): list` | 轉換元素（使用 `value`、`index`） |
| `reduce()` | `list.reduce(expression, initial): any` | 減少為單一值（使用 `value`、`index`、`acc`） |
| `flat()` | `list.flat(): list` | 展平巢狀列表 |
| `join()` | `list.join(separator): string` | 連接為字串 |
| `reverse()` | `list.reverse(): list` | 反轉順序 |
| `slice()` | `list.slice(start, end?): list` | 子列表 |
| `sort()` | `list.sort(): list` | 升序排序 |
| `unique()` | `list.unique(): list` | 移除重複項 |
| `isEmpty()` | `list.isEmpty(): boolean` | 無元素 |

### 檔案函式

| 函式 | 簽名 | 描述 |
|----------|-----------|-------------|
| `asLink()` | `file.asLink(display?): Link` | 轉換為連結 |
| `hasLink()` | `file.hasLink(otherFile): boolean` | 有到檔案的連結 |
| `hasTag()` | `file.hasTag(...tags): boolean` | 有任何標籤 |
| `hasProperty()` | `file.hasProperty(name): boolean` | 有屬性 |
| `inFolder()` | `file.inFolder(folder): boolean` | 在資料夾或子資料夾中 |

### 連結函式

| 函式 | 簽名 | 描述 |
|----------|-----------|-------------|
| `asFile()` | `link.asFile(): file` | 取得檔案物件 |
| `linksTo()` | `link.linksTo(file): boolean` | 連結到檔案 |

### 物件函式

| 函式 | 簽名 | 描述 |
|----------|-----------|-------------|
| `isEmpty()` | `object.isEmpty(): boolean` | 無屬性 |
| `keys()` | `object.keys(): list` | 鍵的列表 |
| `values()` | `object.values(): list` | 值的列表 |

### 正規表達式函式

| 函式 | 簽名 | 描述 |
|----------|-----------|-------------|
| `matches()` | `regexp.matches(string): boolean` | 測試是否匹配 |

## 檢視類型

### 表格檢視

```yaml
views:
  - type: table
    name: "My Table"
    order:
      - file.name
      - status
      - due_date
    summaries:
      price: Sum
      count: Average
```

### 卡片檢視

```yaml
views:
  - type: cards
    name: "Gallery"
    order:
      - file.name
      - cover_image
      - description
```

### 列表檢視

```yaml
views:
  - type: list
    name: "Simple List"
    order:
      - file.name
      - status
```

### 地圖檢視

需要經緯度屬性和 Maps 社群外掛程式。

```yaml
views:
  - type: map
    name: "Locations"
    # 地圖特定設定用於經緯度屬性
```

## 預設摘要公式

| 名稱 | 輸入類型 | 描述 |
|------|------------|-------------|
| `Average` | 數字 | 數學平均值 |
| `Min` | 數字 | 最小數字 |
| `Max` | 數字 | 最大數字 |
| `Sum` | 數字 | 所有數字的總和 |
| `Range` | 數字 | 最大值 - 最小值 |
| `Median` | 數字 | 數學中位數 |
| `Stddev` | 數字 | 標準差 |
| `Earliest` | 日期 | 最早日期 |
| `Latest` | 日期 | 最晚日期 |
| `Range` | 日期 | 最晚 - 最早 |
| `Checked` | 布林值 | true 值的計數 |
| `Unchecked` | 布林值 | false 值的計數 |
| `Empty` | 任何 | 空值的計數 |
| `Filled` | 任何 | 非空值的計數 |
| `Unique` | 任何 | 唯一值的計數 |

## 完整範例

### 任務追蹤器 Base

```yaml
filters:
  and:
    - file.hasTag("task")
    - 'file.ext == "md"'

formulas:
  days_until_due: 'if(due, ((date(due) - today()) / 86400000).round(0), "")'
  is_overdue: 'if(due, date(due) < today() && status != "done", false)'
  priority_label: 'if(priority == 1, "🔴 High", if(priority == 2, "🟡 Medium", "🟢 Low"))'

properties:
  status:
    displayName: Status
  formula.days_until_due:
    displayName: "Days Until Due"
  formula.priority_label:
    displayName: Priority

views:
  - type: table
    name: "Active Tasks"
    filters:
      and:
        - 'status != "done"'
    order:
      - file.name
      - status
      - formula.priority_label
      - due
      - formula.days_until_due
    groupBy:
      property: status
      direction: ASC
    summaries:
      formula.days_until_due: Average

  - type: table
    name: "Completed"
    filters:
      and:
        - 'status == "done"'
    order:
      - file.name
      - completed_date
```

### 閱讀清單 Base

```yaml
filters:
  or:
    - file.hasTag("book")
    - file.hasTag("article")

formulas:
  reading_time: 'if(pages, (pages * 2).toString() + " min", "")'
  status_icon: 'if(status == "reading", "📖", if(status == "done", "✅", "📚"))'
  year_read: 'if(finished_date, date(finished_date).year, "")'

properties:
  author:
    displayName: Author
  formula.status_icon:
    displayName: ""
  formula.reading_time:
    displayName: "Est. Time"

views:
  - type: cards
    name: "Library"
    order:
      - cover
      - file.name
      - author
      - formula.status_icon
    filters:
      not:
        - 'status == "dropped"'

  - type: table
    name: "Reading List"
    filters:
      and:
        - 'status == "to-read"'
    order:
      - file.name
      - author
      - pages
      - formula.reading_time
```

### 專案筆記 Base

```yaml
filters:
  and:
    - file.inFolder("Projects")
    - 'file.ext == "md"'

formulas:
  last_updated: 'file.mtime.relative()'
  link_count: 'file.links.length'
  
summaries:
  avgLinks: 'values.filter(value.isType("number")).mean().round(1)'

properties:
  formula.last_updated:
    displayName: "Updated"
  formula.link_count:
    displayName: "Links"

views:
  - type: table
    name: "All Projects"
    order:
      - file.name
      - status
      - formula.last_updated
      - formula.link_count
    summaries:
      formula.link_count: avgLinks
    groupBy:
      property: status
      direction: ASC

  - type: list
    name: "Quick List"
    order:
      - file.name
      - status
```

### 每日筆記索引

```yaml
filters:
  and:
    - file.inFolder("Daily Notes")
    - '/^\d{4}-\d{2}-\d{2}$/.matches(file.basename)'

formulas:
  word_estimate: '(file.size / 5).round(0)'
  day_of_week: 'date(file.basename).format("dddd")'

properties:
  formula.day_of_week:
    displayName: "Day"
  formula.word_estimate:
    displayName: "~Words"

views:
  - type: table
    name: "Recent Notes"
    limit: 30
    order:
      - file.name
      - formula.day_of_week
      - formula.word_estimate
      - file.mtime
```

## 嵌入 Bases

嵌入在 Markdown 檔案中：

```markdown
![[MyBase.base]]

<!-- 特定檢視 -->
![[MyBase.base#View Name]]
```

## YAML 引號規則

- 對包含雙引號的公式使用單引號：`'if(done, "Yes", "No")'`
- 對簡單字串使用雙引號：`"My View Name"`
- 在複雜表達式中正確跳脫巢狀引號

## 常見模式

### 按標籤過濾
```yaml
filters:
  and:
    - file.hasTag("project")
```

### 按資料夾過濾
```yaml
filters:
  and:
    - file.inFolder("Notes")
```

### 按日期範圍過濾
```yaml
filters:
  and:
    - 'file.mtime > now() - "7d"'
```

### 按屬性值過濾
```yaml
filters:
  and:
    - 'status == "active"'
    - 'priority >= 3'
```

### 結合多個條件
```yaml
filters:
  or:
    - and:
        - file.hasTag("important")
        - 'status != "done"'
    - and:
        - 'priority == 1'
        - 'due != ""'
```

## 參考資料

- [Bases 語法](https://help.obsidian.md/bases/syntax)
- [函式](https://help.obsidian.md/bases/functions)
- [檢視](https://help.obsidian.md/bases/views)
- [公式](https://help.obsidian.md/formulas)

## 報告
在解釋 base 結構、過濾器邏輯或向使用者提供回饋時，您**必須**使用**繁體中文**。
