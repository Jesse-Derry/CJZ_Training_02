# 电控组第二次培训作业

- **陈骏泽**
- ***20263006503***

## 流水灯实现思路

在 CubeMX 中将 PA0、PA2、PA5 配置为输出引脚，并命名为 LED0、LED1、LED2。在代码中通过控制这三个引脚的高低电平，实现三个灯依次亮起的效果。

### 非阻塞延时加分项实现原理

基础写法使用的是 `HAL_Delay(500)`，这会让程序暂停运行500毫秒。为了改进，我使用了 `HAL_GetTick()` 来获取系统当前运行的时间（单位：毫秒），在“查时间”的空隙里，单片机随时可以去执行其他代码。

**具体原理：**
通过计算 `当前时间 % 1500` 的余数，将时间划分为三个500毫秒的阶段。
- 当余数在 0 到 499 时，执行点亮 LED0 的操作。
- 当余数在 500 到 999 时，执行点亮 LED1 的操作。
- 当余数在 1000 到 1499 时，执行点亮 LED2 的操作。
  程序在不断循环判断当前时间，不需要原地暂停等待，实现了非阻塞的流水灯控制。
- （思路由deepseek提供，代码是我自己写的）

### 核心代码

```c
      // 获取当前系统时间
      uint32_t current_time = HAL_GetTick();

      // 先将三个灯都设置为熄灭状态
      HAL_GPIO_WritePin(LED0_GPIO_Port, LED0_Pin, GPIO_PIN_SET);
      HAL_GPIO_WritePin(LED1_GPIO_Port, LED1_Pin, GPIO_PIN_SET);
      HAL_GPIO_WritePin(LED2_GPIO_Port, LED2_Pin, GPIO_PIN_SET);

      // 根据当前时间所在的时间段，点亮对应的灯
      if (current_time % 1500 < 500) {
          HAL_GPIO_WritePin(LED0_GPIO_Port, LED0_Pin, GPIO_PIN_RESET);
      } 
      else if (current_time % 1500 < 1000) {
          HAL_GPIO_WritePin(LED1_GPIO_Port, LED1_Pin, GPIO_PIN_RESET);
      } 
      else {
          HAL_GPIO_WritePin(LED2_GPIO_Port, LED2_Pin, GPIO_PIN_RESET);
      }