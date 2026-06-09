# Banner 规范（生活页 / 司机中心 / 车主圈 / 司机部落）

所有 banner 类场景统一版式骨架：**白底 + 左文字 + 右圆形图**。
横向通用规则见 `rules/banner-circle-mask.md`。

适用场景：
- ✅ 生活页 Banner（hero / card 两个尺寸，**已量化**）
- ✅ 司机中心 Banner（**遵守通用规则，具体尺寸待补充**）
- ✅ 车主圈 Banner（**遵守通用规则，具体尺寸待补充**）
- ✅ 司机部落 Banner（**遵守通用规则，具体尺寸待补充**）

---

## ⚠️ 通用核心硬约束（所有尺寸都遵守）

### 1. 背景必须白色 `#FFFFFF`
不允许全图铺底、不允许渐变蒙层。banner 整体保持白底克制风格。

### 2. 素材图只能放在右侧的圆形区域内
任何元素都不允许越过圆形边界 —— 包括：
- 角色 / 主体溢出
- 阴影外漂
- 装饰花纹、飘带、粒子跨过圆边
- 文字水印超出

实现时**必须用圆形 mask 裁切**，超出部分剪掉。建议 4× 超采样消锯齿。

### 3. 文字区严格在左侧
主标题、副标题、按钮（如有）整体垂直居中，左对齐。

---

## 尺寸版本对照

## 各场景尺寸版本

### 生活页 Banner —— Hero（已量化 ✅，依据官方标注图）

#### 画布与背景
| 维度 | 值 |
|------|------|
| 画布 | **1065 × 300 px** |
| 背景 | 白色 `#FFFFFF` |

#### 水平结构（从左往右）
```
55px │ 文字区 530px │ 55px │ 主视觉区 370px │ 55px
─────┴────────────┴──────┴──────────────┴─────
  左边距            间距     主视觉框         右边距
```

| 维度 | 值 |
|------|------|
| 左边距（画布左 → 文字区左） | **55 px** |
| 文字区宽度 | **530 px**（= 1065 − 55 × 3 − 370） |
| 中间间距（文字区右 → 主视觉区左） | **55 px** |
| 主视觉区宽度 | **370 px** |
| 主视觉区水平位置 | x = 640 ~ 1010 |
| 右边距 | **55 px** |

#### 垂直结构（从上往下）

| 元素 | 占用 | 累计 y |
|------|------|--------|
| 上边距 | 35 px | 0~35 |
| 主标题预留 | 20 px | 35~55 |
| **主标题 bbox** | 67 px | 
| 主副间距（主底 → 副顶） | **25 px** | 125~150 |
| **副标题 bbox** | 36 px | 
| 副→按钮间距（副底 → 按钮顶） | **31 px** | 180~211 |
| **按钮** | **55 px** 高 | 211~266 |
| 下边距 | 35 px | 266~300 |
| ✅ 合计 | 300 px | |

> ⚠️ 主标题 + 副标题 bbox 高度合计 = **99 px**（= 300 − 35 − 20 − 25 − 31 − 55 − 35）。
> 字号选取需让 PIL 实测合计 ≈ 99，推荐 **70pt + 30pt**（汉仪旗黑下 Noto fallback 实测约 100，差 1px 在容差内）。

#### 文字 & 按钮规范

| 元素 | 字号 | 颜色 | 字数限制 |
|------|------|------|---------|
| 主标题 | **67 pt** | `#000000` | **≤ 8 字符** |
| 副标题 | **36 pt** | `#737373`（推荐）| **≤ 12 字符** |
| 按钮文字 | **27 pt**（约） | `#FFFFFF` | **固定 4 字符**（如 "立即参与"） |
| 按钮背景 | 滴滴橙 `#FF6400`（胶囊圆角） | | |

#### 圆形 Mask（强化约束，叠加在主视觉区上）

主视觉区是 370 × 230 矩形，按本 skill 通用规则需替换为**圆形 mask**：

| 维度 | 值 | 推导 |
|------|------|------|
| 圆形直径 | **230 px** | 内接于主视觉区（取短边 = 230） |
| 圆心位置 | **(825, 150)** | 水平居中于主视觉区 |
| 圆形垂直范围 | y = 35 ~ 265 | 上下各 35px 边距 |

#### 垂直放置实现要点（与司机中心同方法）

```python
# 三条精确硬约束：
#  1) 主标题 bbox top = 55  (= 35 + 20)
#  2) 主副间距          = 25  (主底 → 副顶)
#  3) 副→按钮间距       = 31  (副底 → 按钮顶)
#  4) 按钮 bbox bottom = 265 (= 300 - 35)
#
# 用 anchor='lt' + textbbox 实测，与司机中心 banner 同模式

draw.text((PAD_L, 55), TITLE, font=font_t, anchor='lt')
title_bb = draw.textbbox((PAD_L, 55), TITLE, font=font_t, anchor='lt')
sub_y    = title_bb[3] + 25
draw.text((PAD_L, sub_y), SUB, font=font_s, anchor='lt')
sub_bb   = draw.textbbox((PAD_L, sub_y), SUB, font=font_s, anchor='lt')
btn_top  = sub_bb[3] + 31
# 按钮高 55，bottom = btn_top + 55 ≈ 265 (= H - 35) ✓
```

---

### 生活页 Banner —— Card（已量化 ✅）

| 维度 | 值 |
|------|------|
| 画布 | **710 × 150** |
| 圆形直径 | 115 px |
| 圆形圆心 | (610, 75) |
| 圆形右边距 | 44 px |
| 文字左边距 | 30 px |
| 主标题 | 67 pt / `#000000` / **≤ 10 字** |
| 副标题 | 36 pt / `#737373` / **≤ 8 字** + 末尾 ` >` |
| 跳转入口 | 副标题尾部 ` >` 箭头（**无按钮**） |

### 选版式的判断口诀
- 需要按钮 / 主≤8 字 / 副有完整短句 → **Hero**
- 文案极简 / 一句话跳转 → **Card**


### 司机中心 Banner（已量化 ✅）

| 维度 | 值 | 备注 |
|------|------|------|
| 画布 | **750 × 230 px** | 官方规范 |
| 输出格式 | JPG / PNG / GIF | 官方规范 |
| 文件大小 | **≤ 300 KB**（越小越好） | 官方规范 |
| 背景 | 白色 `#FFFFFF` | 通用规则 |
| 圆形直径 | **180 px** | 强化约束 |
| 圆形上/下/右边距 | 25 / 25 / 50 px | 25+180+25=230 ✓ |
| 圆形圆心 | (610, 115) | 推导值 |
| 文字左边距 | 35 px | |
| 文字右边界 | ≤ 520 px | 不得越过圆形左边 |
| 主标题 | 36pt / `#000000` / ≤12 字 | 汉仪旗黑 |
| 副标题 | 27pt / `#737373` / ≤16 字 | 汉仪旗黑 |

#### ⚠️ 垂直对齐硬约束（按这三条精确放置文字）

1. **主标题 bbox top 距画布顶 = 80 px**
2. **主副行距（主标题 bbox bottom → 副标题 bbox top）= 9 px**
3. **副标题 bbox bottom 距画布底 = 80 px**

实现要点：PIL 用 `draw.text(xy, ..., anchor='lt')` 让 `y` 严格等于 bbox 顶；副标题 y 从主标题 `textbbox` 实测 bottom + 9 起算。

**渲染容差**：数学上 80+36+9+27+80 = 232，超画布 2px，叠加字体渲染 1–2px 浮动，**副标题距下实测会落在 76–78px**，属于不可见容差，可接受。

#### ⚠️ 按钮规则（按官方规范严格执行）

1. **有跳转落地页** → 必须出现引导按钮
2. **无跳转落地页** → **禁止出现按钮**或任何点击效果

> 司机中心 banner **默认按"无按钮"处理**。仅当用户明确说"有落地页 / 需要按钮"时才放按钮。
> 这跟生活页 Hero（默认放按钮）相反，**不要弄混**。

#### 字体 fallback
- 汉仪旗黑 → `NotoSansCJK-Black.ttc`（主） / `NotoSansCJK-Medium.ttc`（副）
- Aspira → 系统默认英文字体

### 车主圈 Banner（待量化 ⏳）

通用约束已生效：**白底 + 左文字 + 右圆形 mask**。
具体画布尺寸、圆形直径、字号、字数限制等参数待补充。

### 司机部落 Banner（待量化 ⏳）

通用约束已生效：**白底 + 左文字 + 右圆形 mask**。
具体画布尺寸、圆形直径、字号、字数限制等参数待补充。

---

### 生活页选版式的判断口诀
- 文案里**有按钮诉求** / 主标题 ≤8 字 / 副标题需要完整短句 → **Hero**
- 文案极简 / 只需要一句标题+一个跳转 → **Card**

---

## 生成代码骨架（生活页 Hero 1065×300，按官方标注）

```python
from PIL import Image, ImageDraw, ImageFont

W, H        = 1065, 300
PAD_L       = 55                          # 文字区左边距（官方）
CIRCLE_DIAM = 230                         # 圆形直径
CIRCLE_CX   = 640 + 370 // 2              # 825（主视觉区水平中点）
CIRCLE_CY   = 150                         # 垂直中点

# 垂直硬约束
TITLE_TOP   = 55      # = 35 + 20
GAP_TS      = 25      # 主底 → 副顶
GAP_SB      = 31      # 副底 → 按钮顶
BTN_H       = 55
# 按钮底 ≈ H - 35 = 265

canvas = Image.new("RGB", (W, H), "#FFFFFF")

# === 右侧圆形图（4× 超采样 mask 消锯齿） ===
art = Image.open(art_path).convert("RGB")
side = min(art.width, art.height)
lx = (art.width - side) // 2
ly = int(art.height * 0.31)
art_sq = art.crop((lx, ly, lx+side, ly+side)).resize(
    (CIRCLE_DIAM, CIRCLE_DIAM), Image.LANCZOS)

SUP = 4
mb = Image.new("L", (CIRCLE_DIAM*SUP, CIRCLE_DIAM*SUP), 0)
ImageDraw.Draw(mb).ellipse((0, 0, CIRCLE_DIAM*SUP, CIRCLE_DIAM*SUP), fill=255)
mask = mb.resize((CIRCLE_DIAM, CIRCLE_DIAM), Image.LANCZOS)
canvas.paste(art_sq,
             (CIRCLE_CX - CIRCLE_DIAM//2, CIRCLE_CY - CIRCLE_DIAM//2),
             mask)

# === 左侧文字（anchor='lt' + textbbox 实测，三条硬约束） ===
FONT_DIR = "/Users/coco/Desktop/DIDI资源位/fonts"
font_t = ImageFont.truetype(f"{FONT_DIR}/HYQiHei-70S.otf", 67)
font_s = ImageFont.truetype(f"{FONT_DIR}/HYQiHei-50S.otf", 36)
font_b = ImageFont.truetype(f"{FONT_DIR}/HYQiHei-55S.otf", 27)
draw   = ImageDraw.Draw(canvas)

draw.text((PAD_L, TITLE_TOP), TITLE, font=font_t, fill="#000000", anchor='lt')
tb = draw.textbbox((PAD_L, TITLE_TOP), TITLE, font=font_t, anchor='lt')

sub_y = tb[3] + GAP_TS
draw.text((PAD_L, sub_y), SUB, font=font_s, fill="#737373", anchor='lt')
sb = draw.textbbox((PAD_L, sub_y), SUB, font=font_s, anchor='lt')

# 按钮（固定 4 字符，高 55，胶囊圆角）
btn_y = sb[3] + GAP_SB
bb = draw.textbbox((0, 0), BTN, font=font_b)
btw = bb[2] - bb[0]
btn_w = btw + 48    # 左右各 24 padding
btn_h = BTN_H
draw.rounded_rectangle([PAD_L, btn_y, PAD_L+btn_w, btn_y+btn_h],
                       radius=btn_h//2, fill="#FF6400")
draw.text((PAD_L + (btn_w-btw)//2 - bb[0],
           btn_y + (btn_h-(bb[3]-bb[1]))//2 - bb[1]),
          BTN, font=font_b, fill="#FFFFFF")
```

---

## 历史版本对照（防错）

❌ 之前出现过的错误版式：
- 全图铺底 + 左侧白色渐变蒙层 + 文字叠在铺底图上
- 这种"hero 大图"做法是 **错误的**，生活页 banner 一律白底+圆形右图
