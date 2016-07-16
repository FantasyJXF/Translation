# Pixhawk系列飞控板的协同计算机

无论何种协同计算机（Raspberry Pi, Odroid, Tegra K1），与Pixhawk系列飞控板之间的接口是相同的：它们通过串口连接到Pixhawk上的`TELEM2`，这个端口专用于与协同计算机相连。连接的消息格式是[MAVLink](http://mavlink.org)。

## Pixhawk设置

参考下表，设置`SYS_COMPANION`参数（System参数组）

<aside class="tip">
变更参数后需要重启飞控使其生效。
</aside>

- `0`：禁用TELEM2上的MAVLink输出（默认）
- `921600`：使能MAVLink输出，波特率：921600, 8N1（推荐）
- `157600`：使能MAVLink输出，OSD模式，波特率：57600
- `257600`：使能MAVLink输出，监听模式，波特率：57600

## 协同计算机设置

为了能够接收MAVLink消息，协同计算机需要运行一些和串口通讯的软件，最常用的是：

- [MAVROS](../10_Robotics-using-ROS/ros-mavros-installation.md)：ROS
- [C/C++ example code](https://github.com/mavlink/c_uart_interface_example)：自定义的代码
- [MAVProxy](http://mavproxy.org)：在串口和UDP之间传输MAVLink

## 硬件设置

根据下面的说明连接串口。所有Pixhawk串口工作在3.3V，兼容5V。

<aside class="caution">
许多现代协同计算机在UART端口仅支持1.8V的电压，并且可能在3.3V下损坏。使用电压转换器。大多数时候，可以使用的硬件串口有特定的功能（modem or console），在使用之前，需要在Linux下重新配置它们。
</aside>
安全的做法是使用FTDI（USB转串口适配器），并按照下面说明连接它。这大多数时候都管用并且很容易设置。

| TELEM2   |          | FTDI    |                         |
--- | --- | ---
|1         | +5V (red)|         | DO NOT CONNECT!         |
|2         | Tx  (out)| 5       | FTDI RX (yellow) (in)   |
|3         | Rx  (in) | 4       | FTDI TX (orange) (out)  |
|4         | CTS (in) |6        | FTDI RTS (green) (out)  |
|5         | RTS (out)|2        | FTDI CTS (brown) (in)   |
|6         | GND      | 1       | FTDI GND (black)        |
