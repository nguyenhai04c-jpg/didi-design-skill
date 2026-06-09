# name: 开屏类型映射
> 在执行任何开屏生成任务前，先读取本文件确认版式，再加载对应 skill。

---

## 类型映射表

| 用户说的开屏类型 | 对应版式 | 读取 skill |
|----------------|---------|-----------|
| 常规开屏 | 版式一（标准版） | `didi-splash-v1` |
| 营销开屏 | 版式二（活力版） | `didi-driver-splash` |

---

## 识别规则

**走版式一（常规开屏）的关键词：**
- 「常规开屏」「标准版」「旗黑」「正式」「稳重」「版式一」

**走版式二（营销开屏）的关键词：**
- 「营销开屏」「活力版」「活力黑」「促销」「活动」「版式二」

**未明确指定时：** 询问用户「请问是常规开屏还是营销开屏？」，确认后再加载对应 skill。

---

## 代码映射

```python
SPLASH_TYPE_MAP = {
    "常规开屏": {
        "版式":    "版式一（标准版）",
        "字体":    "汉仪旗黑",
        "skill":  "didi-splash-v1",
        "风格":    "正式、稳重",
    },
    "营销开屏": {
        "版式":    "版式二（活力版）",
        "字体":    "滴滴活力黑体",
        "skill":  "didi-driver-splash",
        "风格":    "活力、促销",
    },
}
```

## 标题字色自动映射规则

在未明确指定标题颜色时，根据素材背景亮度自动判断：

| 背景类型 | 标题默认色 | 色值 |
|----------|-----------|------|
| 浅色背景 | 黑色 | `#000000` |
| 深色背景 | 白色 | `#FFFFFF` |

如需使用滴滴桔 `#FF6400`，须由用户明确指定。

### 亮度判断方法（Python）

```python
import numpy as np
from PIL import Image

def detect_bg_brightness(img: Image.Image, sample_region: tuple = None) -> str:
    """
    判断背景亮度，返回 'light' 或 'dark'。
    sample_region: (x1, y1, x2, y2)，默认取文案编辑区左上角采样。
    """
    img_rgb = img.convert("RGB")
    if sample_region:
        img_rgb = img_rgb.crop(sample_region)
    arr = np.array(img_rgb, dtype=np.float32)
    # 感知亮度公式
    luminance = (arr[:,:,0]*0.299 + arr[:,:,1]*0.587 + arr[:,:,2]*0.114).mean()
    return "light" if luminance > 128 else "dark"

# 使用
bg_type     = detect_bg_brightness(canvas, sample_region=(0, 120, 640, 366))
TITLE_COLOR = "#000000" if bg_type == "light" else "#FFFFFF"
```