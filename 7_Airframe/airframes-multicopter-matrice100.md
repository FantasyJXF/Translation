# Matrice 100

# Matrice 100

{% youtube %}https://www.youtube.com/watch?v=3OGs0ONemGc{% endyoutube %}

![Matrice 100](../pictures/airframes/multicopter/matrice100/Matrice100.jpg)

## 部件列表

- [DJI Matrice 100](http://store.dji.com/product/matrice-100) 仅包含电调，电机和机架。

## 电机连接

![Connections](../pictures/airframes/multicopter/matrice100/Wiring Diagram.jpg)

![Wiring Harness](../pictures/airframes/multicopter/matrice100/WiringHarness.jpg)

![PWM Connections](../pictures/airframes/multicopter/matrice100/PwmInput.jpg)

![Top](../pictures/airframes/multicopter/matrice100/Top.jpg)

![Back](../pictures/airframes/multicopter/matrice100/Back.jpg)

![No Stack](../pictures/airframes/multicopter/matrice100/NoStack.jpg)

![No Top Deck](../pictures/airframes/multicopter/matrice100/NoTopDeck.jpg)

| 输出    | 频率   | 作动器            |
| ------ | ------ | ---------------- |
| MAIN1  | 400 Hz | 右前方，逆时针     |
| MAIN2  | 400 Hz | 左后方，逆时针     |
| MAIN3  | 400 Hz | 左前方，顺时针     |
| MAIN4  | 400 Hz | 右后方，顺时针     |
| AUX1   | 50 Hz  | RC AUX1          |
| AUX2   | 50 Hz  | RC AUX2          |
| AUX3   | 50 Hz  | RC AUX3          |

## 参数

- 如果使用默认的四旋翼X型布局的增益，那么在高油门的时候，将会发生姿态震荡。在低油门的时候，较高的增益会获得比较好的响应。这说明，采用基于油门的增益调度可能会提高全油门的响应性能，这可以在mc_att_control中实现。目前，我们仅仅调整增益使得在低油门和高油门的时候不会出现震荡，在低油门的时候达到带宽要求。
  - MC_PITCHRATE_P: 0.05
  - MC_PITCHRATE_D: 0.001
- 电池有6个单元，而不是默认的3个
  - BAT_N_CELLS: 6