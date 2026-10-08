# LVGL（Light and Versatile Graphics Library 图形库）学习导读

> 目录：`03-图形显示/lvgl-LVGL图形库`
> 来源：https://github.com/lvgl/lvgl （压缩包：lvgl-master.zip）

## 这是什么

LVGL 是单片机世界最流行的开源图形库：对象/控件模型（类似 Android 的 View 树）、动画、主题、抗锯齿字体、触摸输入，甚至还有官方可视化编辑器（EEZ Studio / SquareLine）。STM32+FreeRTOS+LVGL 是当前智能设备 UI 的标配组合（你手上的 OV-Watch 就在用）。支持 16/24/32 位色、BLE/触摸各种输入，最低 64KB Flash / 16KB RAM 即可跑起来。

## 目录导读

| 目录/文件 | 内容 |
| --- | --- |
| `src/core/` | 对象系统核心：`lv_obj.c`（一切控件之祖）、布局、事件 |
| `src/display/` `src/indev/` | 显示驱动接口、输入设备接口（触摸/编码器/键盘） |
| `src/widgets/` | 全部控件：label、btn、arc、chart、tabview、roller... |
| `src/draw/` | 渲染引擎（软件绘制 + GPU/硬件加速 hook，如 PXP、DMA2D） |
| `src/font/` `src/libs/` | 字体与第三方库（FT6336、qrcode、ffmpeg、freetype...） |
| `examples/` | 官方示例（每个控件都有可跑的小例子） |
| `demos/` | 大型演示（widgets、music、benchmark、stress） |
| `docs/` | 官方文档源码（在线版 api.livgl.io） |
| `env_support/` | 各平台移植辅助（cmake、keil、esp、zephyr...） |

## 学习路径（建议）

1. **先用 PC 模拟器跑通**（强烈推荐）：官方 `lv_port_pc_vscode`（基于 SDL2），Windows/Linux 均可，改代码秒看效果，不用买任何硬件。
2. 依次读 examples：`hello_world` → label/btn → 事件回调 → 布局（flex/grid）→ 动画 → `demos/music`（看完就知道工业级 UI 怎么写）。
3. 读核心机制三件事：**刷新流程**（`lv_timer_handler` → 重绘脏区）、**渲染管线**（`src/draw/`）、**输入事件分发**（indev → obj 事件冒泡）。
4. 上板：STM32F4 + SPI/FSMC 屏（ST7789/ILI9341），或 ESP32 + SPI 屏（`lv_port_esp32` 官方模板）。

## 学习要点

1. ** lv_timer_handler() 是心跳**：放到主循环或 RTOS 任务里每 5-10ms 调一次，一切动画/刷新由此驱动。
2. **脏区重绘**：LVGL 只重绘变化区域，理解 `lv_obj_invalidate` 才能排查"刷新慢"。
3. **内存策略**：内置 `lv_mem`（静态池）或自定义 malloc；控件树 + 样式都在堆上。
4. **中文显示**：用官方工具（lv_font_conv）生成含汉字子集的字体文件（UTF-8 编码）。
5. **性能调优**：DMA2D/双缓冲/`LV_CACHE_DEF_SIZE`，`demos/benchmark` 量化对比。

## 建议购买/准备

- **零成本**：PC SDL2 模拟器学完 80% 的内容。
- **上板推荐**：
  - ESP32-2432S028（俗称"2.8寸小黄板"，¥40-60，触摸+屏+ESP32 一体，性价比之王）
  - 或 STM32F407 核心板（¥60-100）+ 2.8/3.5 寸 ILI9341 带触摸屏（¥30-60）
  - 或直接买现成智能手表套件
- 数据线即可开跑。

## 学习目标（自测）

- [ ] PC 模拟器跑通 demos/music
- [ ] 能讲清一次按钮点击从触摸到回调的完整事件链
- [ ] 会用 flex 布局搭建一个带页面切换的多页 UI
- [ ] 在真机上点亮屏幕并达到 30+ FPS
