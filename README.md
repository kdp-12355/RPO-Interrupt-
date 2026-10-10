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
