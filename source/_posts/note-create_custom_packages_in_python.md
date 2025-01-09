---
title: python创建自定义包
date: 2025-01-09 16:37:01
tags: [-Python]
categories: -笔记
---

<h1 style="font-size: 20px;">python中自定义包时，初始化文件的书写规范</h1>

### 1. 项目结构示例

- **`SmodernUI/`**:

```text
SmodernUI/
│
├── component/
│   ├── ui/
│   │   ├── __init__.py
│   │   ├── Ui_message.py
│   │   └── Ui_tip.py
│   │
│   ├── widget/
│   │   ├── __init__.py
│   │   ├── button.py
│   │   └── tip.py
│   │
│   └── window/
│       ├── __init__.py
│       └── message.py
│
├── core/
│   ├── __init__.py
│   ├── color.py
│   ├── globals.py
│   ├── animation.py
│   └── func.py
│
├── resource/
│   ├── __init__.py
│   └── resource_rc.py
│
└── __init__.py
```

### 2. 根目录的 `__init__.py` 文件

在根目录下，你可以添加一个 `__init__.py` 文件导入整个库的核心模块。这样，用户只需导入库本身时，就能够轻松访问库中的重要功能。

- **`SmodernUI/__init__.py`**:

```python
# 导入核心模块
from .core.color import *
from .core.animation import *
from .core.func import *
from .core.globals import *

# 导入组件模块
from .component.ui.Ui_message import *
from .component.ui.Ui_tip import *

from .component.widgets.button import *
from .component.widgets.tip import *

from .component.window.message import *

# 可以根据需要导入更多的模块或函数
```

### 3. 子包中的 `__init__.py` 文件

比如在子包 `component/` ，在`component/`目录下的每个子目录（例如 `ui/`, `widget/`, `window/`）中添加一个 `__init__.py` 文件，以便更清晰地组织每个子包。

- **`component/ui/__init__.py`**:
  
  ```python
  # component/ui/__init__.py

  # 导入UI相关的模块
  from .UI_message import *
  from .UI_tip import *
  ```

- **`component/widget/__init__.py`**:

  ```python
  # component/widget/__init__.py

  # 导入Widget相关的模块
  from .button import *
  from .tip import *
  ```

- **`component/window/__init__.py`**:

  ```python
  # component/window/__init__.py

  # 导入window相关的模块
  from .message import *
  ```

同样地，在 `core/` 目录下，你可以添加一个 `__init__.py` 文件来导入相关的核心模块。

- **`core/__init__.py`**:

  ```python
  # core/__init__.py

  # 导入核心功能模块
  from .color import *
  from .animation import *
  from .func import *
  ```

### 3. 模块内部的互相导入

在模块内部，你可以使用相对导入来导入其他模块。例如，在 `message.py` 中，你可以这样导入 `Ui_message.py`，`globals.py`：

- **`component/ui/Ui_message.py`**:

```python
from ..ui import Ui_message 或者 from ..ui.Ui_message import *
from ...core import Globals 或者 from ...core.Globals import *
```

### 4. 外部导入方法

假设已经按照上述方式创建了 `__init__.py` 文件，现在可以方便地导入模块。

```python
import SmodernUI

# 你可以直接使用库中的模块和类
SmodernUI.color.some_color_function()
SmodernUI.UI_message.some_function_in_UI_message()
SmodernUI.button.some_button_function()
```

如果只需要部分功能，也可以按需导入：

```python
from SmodernUI import color, UI_message, button
```

### 总结

- 在每个子目录中创建 `__init__.py` 文件来组织包内模块的导入。
- 在根目录的 `__init__.py` 中导入核心模块和常用功能，提供简洁的访问方式。
