# 品牌总则（指导规则）
> DiDi Brand Guidelines 2025（Banner）/ 2026（开屏）

## 执行流程（强制顺序）

| 步骤 | 动作 |
|------|------|
| 0 | 读取本文件（品牌总则） |
| 1 | 按场景读取对应 reference/ 规范细则 |
| 2 | 对照「产出前检查清单」逐项确认 |
| 3 | 裁切 / 合成 / 输出文件 |

---

## 产出前检查清单

- [ ] 已读取品牌总则（本文件）
- [ ] 已读取对应场景规范（reference/ 下对应文件）
- [ ] 画布尺寸与场景一致
- [ ] 标题字数未超限，标题区无副标题
- [ ] 按钮：有落地页必须有 CTA；无落地页不得出现按钮
- [ ] 主视觉仅在规范区域内，不遮挡文案
- [ ] 字体正确（中文汉仪旗黑 / 英文数字 Aspira / 活力版滴滴活力黑）
- [ ] 文件格式与大小在规范上限内
- [ ] 未擅自添加装饰、二维码、假 App 壳、参考线框

---

## 色彩规范

| 项 | 颜色 |
|----|------|
| 主品牌色（CTA / 强调） | DiDi Orange `#FF6400` |
| 文案色（浅色背景） | 黑 `#000000` |
| 文案色（深色背景） | 白 `#FFFFFF` |
| 开屏标题允许色 | `#FF6400` / `#FFFFFF` / `#000000` / `#333333` |

---

## 字体规范

### Banner 字体（系统字体路径）

```python
FONT_DIR = "/Users/coco/Desktop/DIDI资源位/fonts"

font_title = ImageFont.truetype(f"{FONT_DIR}/HYQiHei-70S.otf", size)   # 主标题
font_sub   = ImageFont.truetype(f"{FONT_DIR}/HYQiHei-50S.otf", size)   # 副标题
font_btn   = ImageFont.truetype(f"{FONT_DIR}/HYQiHei-55S.otf", size)   # 按钮
```

| 文件 | 用途 |
|------|------|
| HYQiHei-70S.otf | Banner 主标题 |
| HYQiHei-50S.otf | Banner 副标题 |
| HYQiHei-55S.otf | Banner 按钮 / 辅助 |
| Aspira Regular.otf | 英文与数字 |

### 开屏字体（项目字体路径）

```python
PROJECT_FONTS = "/Users/coco/Desktop/DIDI资源位/fonts"

font_v1_title = ImageFont.truetype(f"{PROJECT_FONTS}/HYQiHei-75W.otf", size)         # 版式一主标题
font_v2_title = ImageFont.truetype(f"{PROJECT_FONTS}/滴滴活力黑-Regular.otf", size)   # 版式二主标题
```

| 文件 | 用途 |
|------|------|
| HYQiHei-75W.otf | 开屏版式一主标题 |
| 滴滴活力黑-Regular.otf | 开屏版式二主标题 |

> 严禁使用 NotoSansCJK、DejaVu、PingFang 等替代字体用于正式交付稿。

---

## 文件输出通则

| 类型 | 格式 | 大小上限 |
|------|------|---------|
| Banner | JPG（静态）/ PNG（透明）/ GIF | ≤ 300 KB |
| 开屏 A | JPG / PNG | ≤ 200 KB |
| 开屏 B / C | JPG / PNG | ≤ 500 KB |

- 优先 JPG；需透明区域用 PNG
- 默认输出目录：`/Users/coco/Desktop/DIDI资源位/outputs/`

---

## 版式原则（Banner / 开屏共通）

- 结构：**文案区 + 单一主视觉区**，保持留白，勿做成海报或社交图
- 1 字符位 = 1 个中文 / 英文 / 数字 / 标点
- 主视觉与文案分区明确，禁止主图压过标题区
- 禁止擅自添加装饰、贴纸、多余 UI 元素

---

## Logo 路径

```python
DIDI = "/Users/coco/Desktop/DIDI"
LOGO_TOP = f"{DIDI}/logos/矢量智能对象.png"   # 开屏顶部组合 logo
```

---

## 免责声明

规范示例仅供参考。图像素材须取得合法授权后方可使用。色彩及字体须严格遵循 DiDi Brand Guidelines 2025/2026。
