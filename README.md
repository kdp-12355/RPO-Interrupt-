# RPO-Interrupt-


回放无课程组
cd /workspace/IsaacLab

export DISPLAY=:1
export OMNI_KIT_ALLOW_ROOT=1

./isaaclab.sh -p \
  /workspace/robotor/roboparty_train-main/robolab/scripts/rsl_rl/play.py \
  --task RPO-Interrupt-NoCurr \
  --checkpoint /workspace/IsaacLab/logs/rsl_rl/rpo_interrupt_nocurr/2026-10-09_14-33-18_nocurr_final/model_8999.pt \
  --num_envs 1 \
  --device cuda:0




用无中断组模型，在 RPO-Interrupt 环境中回放

  cd /workspace/IsaacLab

export DISPLAY=:1
export OMNI_KIT_ALLOW_ROOT=1

./isaaclab.sh -p \
  /workspace/robotor/roboparty_train-main/robolab/scripts/rsl_rl/play.py \
  --task RPO-Interrupt \
  --checkpoint /workspace/IsaacLab/logs/rsl_rl/rpo_interrupt_noint/2026-10-09_02-37-17/model_8999.pt \
  --num_envs 1 \
  --device cuda:0





NoCurr 固定等级评估也已完成，成功生成 CSV，且评估规模与 Full 一致：32 个环境、每环境 10,000 步、共 320,000 个速度跟踪样本。

一个值得关注的初步发现是：NoCurr 的速度跟踪误差略低，但平均恢复时间更长，非超时终止率也更高。 这说明课程学习的作用可能主要体现在中断后的恢复过程，而不是所有速度跟踪指标上；不过还需要结合 NoInt 结果，才能判断这一趋势是否支持你的研究假设。

## 1. Full 与 NoCurr 定量对比

两组均采用固定中断等级 1.0，评估条件一致。

| 指标               | Full   | NoCurr | 较优结果    |
| ---------------- | ------ | ------ | ------- |
| 非超时终止率 ↓         | 19.53% | 22.62% | Full    |
| 超时率 ↑            | 80.47% | 77.38% | Full    |
| \\(v_x\\) MAE ↓  | 0.1071 | 0.0979 | NoCurr  |
| \\(v_x\\) RMSE ↓ | 0.1697 | 0.1569 | NoCurr  |
| \\(v_y\\) MAE ↓  | 0.1165 | 0.1152 | NoCurr  |
| \\(v_y\\) RMSE ↓ | 0.1565 | 0.1551 | NoCurr  |
| \\(w_z\\) MAE ↓  | 0.1681 | 0.1726 | Full    |
| \\(w_z\\) RMSE ↓ | 0.2639 | 0.2825 | Full    |
| 平均恢复时间（秒）↓       | 0.8209 | 0.9913 | Full    |
| 恢复时间中位数（秒）↓      | 0.500  | 0.600  | Full    |
| 中断开始次数           | 781    | 775    | —       |
| 恢复成功次数           | 528    | 532    | 暂不宜直接比较 |

说明：速度跟踪误差越低越好，恢复时间越短越好。箭头表示理想方向。

## 2. 结果说明了什么？

- 速度跟踪： NoCurr 的前向和侧向跟踪误差略低，但角速度跟踪较差。
- 恢复能力： Full 的平均恢复时间约为 0.821 秒，NoCurr 约为 0.991 秒，Full 快约 17.2%。这是目前支持课程学习有益于恢复能力的主要证据。
- 稳定性： Full 的非超时终止率比 NoCurr 低约 3.09 个百分点，方向上也支持 Full，但还需要统计不确定性分析。
- 恢复成功次数： Full 和 NoCurr 的中断结束次数分别是 756 和 751，样本量接近，但恢复成功次数不能脱离具体的截尾与成功判定逻辑单独解释。

目前每组只有一次评估运行，且没有多随机种子统计，因此应把这些结论称为初步实验观察，而不是已经证明的统计显著优势。

## 3. 三组消融实验汇总

将之前的 NoInt 结果也放进来，可以得到下面这张初步表格。

| 指标              | Full     | NoInt  | NoCurr   |
| --------------- | -------- | ------ | -------- |
| 非超时终止率 ↓        | 19.53%   | 1.25%  | 22.62%   |
| \\(v_x\\) MAE ↓ | 0.1071   | 0.1358 | 0.0979   |
| \\(v_y\\) MAE ↓ | 0.1165   | 0.1240 | 0.1152   |
| \\(w_z\\) MAE ↓ | 0.1681   | 0.1399 | 0.1726   |
| 平均恢复时间 ↓        | 0.8209 s | —      | 0.9913 s |

NoInt 的中断恢复时间没有可直接对照的数据，因为它没有中断事件。它的低终止率也不能单独说明策略整体更优：它没有承担 Full 和 NoCurr 所承受的中断扰动。

论文中建议的下一步： 对 Full、NoInt、NoCurr 各使用至少 3–5 个随机种子重复评估，报告均值和标准差；同时检查恢复成功率的统计分母，并确保三个模型使用一致的命令速度、地形、仿真时长和中断强度。这样才能更有力地论证课程学习对抗扰恢复能力的贡献。




很好，现在 `interrupt_ablation_table.tex` 已经正确。

检查结果：

✅ 列数一致：

```latex
\begin{tabular}{lcccccc}
```

对应：

|列|内容|
|-|-|
|1|Method|
|2|Fall Rate|
|3|$v_x$ MAE|
|4|$v_y$ MAE|
|5|$\omega_z$ MAE|
|6|Recovery Rate|
|7|Recovery Time|

三组数据均为 7 项，没有错位。

---

## 下一步 1：编译 PDF

先检查 LaTeX：

```bash
which pdflatex
```

如果输出路径，例如：

```text
/usr/bin/pdflatex
```

执行：

```bash
pdflatex interrupt_ablation_table.tex
```

成功后：

```bash
ls -lh interrupt_ablation_table.*
```

应该看到：

```
interrupt_ablation_table.tex
interrupt_ablation_table.pdf
```

---

## 下一步 2：如果没有 pdflatex

如果：

```bash
which pdflatex
```

没有输出，不安装也没关系。

目前你已经有：

```
interrupt_ablation_table.csv
```

和：

```
interrupt_ablation_table.tex
```

论文写作已经够用。

---

## 下一步 3：建议整理论文目录

现在建议整理：

```bash
cd /workspace/IsaacLab/logs/rsl_rl/interrupt_eval

mkdir -p paper_results

mv interrupt_ablation_table.csv paper_results/
mv interrupt_ablation_table.tex paper_results/

mv paper_figures paper_results/
```

最终：

```
interrupt_eval/

paper_results/
│
├── interrupt_ablation_table.csv
├── interrupt_ablation_table.tex
│
└── paper_figures/
    ├── fall_rate.pdf
    ├── recovery_rate.pdf
    └── recovery_time.pdf
```

这个目录可以直接作为论文实验附件。

---

## 下一步建议：制作 RPO-Interrupt 视频实验

目前定量结果：

- 800 次扰动
- recovery success 92.02%
- recovery time 0.369s

已经够表格。

论文还缺：

**定性展示（qualitative result）**

建议做一个 10 秒视频：

流程：

```
0s-2s:
normal walking

2s:
apply arm joint disturbance

2s-3s:
body deviation

3s-5s:
recover velocity

5s-10s:
stable walking
```

同时记录：

- base height
- roll/pitch
- commanded velocity
- actual velocity

生成一张：

```
Fig. Disturbance recovery behavior
```

这会明显增强 RPO-Interrupt 部分。

你现在可以进入 `sim2sim/play` 阶段。
