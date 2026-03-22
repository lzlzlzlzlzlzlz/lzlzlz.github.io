# 🛠️ 前置准备：系统与环境配置指南 (OmniRobCtrl)

> **📝 元数据**
> - **适用系统**: Ubuntu 20.04 (云服务器/本地)
> - **核心环境**: Python 3.8.10, CUDA 11.x
> - **关键组件**: Isaac Gym, PyTorch, MuJoCo
> - **最后更新**: 2023-10-27
> - **状态**: ✅ 验证通过

---

## 1. 基础环境要求

- **操作系统**: Ubuntu 20.04
  > *注：具体搭建过程教程众多，请根据自身情况自行搜索安装。云服务器通常已预装。*

## 2. Anaconda 虚拟环境设置

首先创建并激活专用的 Python 虚拟环境，以确保依赖包的隔离性，避免污染系统环境。

```bash
# 1. 创建名为 OmniRobCtrl 的虚拟环境，锁定 Python 版本为 3.8.10
conda create -n OmniRobCtrl python=3.8.10 -y

# 2. 激活该环境
conda activate OmniRobCtrl

#3. 配置系统环境变量 (.bashrc)
nano ~/.bashrc
# 或者使用 vim ~/.bashrc

# ==========================================
# OmniRobCtrl 环境专属配置
# ==========================================

# 1. CUDA 路径配置
export PATH=/usr/local/cuda/bin: $ PATH
export LD_LIBRARY_PATH=/usr/local/cuda/lib64: $ LD_LIBRARY_PATH

# 2. 系统基础库路径
export LD_LIBRARY_PATH=/usr/lib/x86_64-linux-gnu: $ LD_LIBRARY_PATH

# 3. Conda 环境库路径 (⚠️ 需修改此处)
# 请将下方路径替换为您实际的 conda 安装绝对路径
# 示例：/home/your_username/anaconda3/envs/OmniRobCtrl/lib/
# 如果您使用的是 miniconda，请将 anaconda3 改为 miniconda3
export LD_LIBRARY_PATH=~/anaconda3/envs/OmniRobCtrl/lib/: $ LD_LIBRARY_PATH

# 4. Vulkan 配置 (NVIDIA 显卡必备，用于图形渲染)
export VK_ICD_FILENAMES=/usr/share/vulkan/icd.d/nvidia_icd.json

source ~/.bashrc

#4. 安装 Python 依赖包
# --- 深度学习核心 ---
pip install torchvision torch -i https://pypi.tuna.tsinghua.edu.cn/simple

# --- 数学与物理引擎 ---
pip install pyquaternion pyyaml -i https://pypi.tuna.tsinghua.edu.cn/simple
pip install mujoco==2.3.7 mujoco-py mujoco-python-viewer -i https://pypi.tuna.tsinghua.edu.cn/simple
pip install dm_control==1.0.14 -i https://pypi.tuna.tsinghua.edu.cn/simple

# --- 机器人工具链 ---
pip install rospkg lcm -i https://pypi.tuna.tsinghua.edu.cn/simple

# --- 可视化与通用工具 ---
pip install opencv-python matplotlib einops tqdm h5py tensorboard ipython -i https://pypi.tuna.tsinghua.edu.cn/simple
pip install wandb getkey chardet packaging pexpect -i https://pypi.tuna.tsinghua.edu.cn/simple
pip install h5py_cache -i https://pypi.tuna.tsinghua.edu.cn/simple

# --- 版本锁定 (防止兼容性报错) ---
pip install numpy==1.23.2 -i https://pypi.tuna.tsinghua.edu.cn/simple

#5. 安装与配置 Isaac Gym
下载 NVIDIA Isaac Gym 压缩包并解压后，进入 Python 目录进行安装。
# 1. 进入 Isaac Gym 的 python 目录
cd isaacgym/python

# 2. 以可编辑模式安装 (-e 参数便于后续开发调试，无需重新安装即可生效代码修改)
pip install -e .

效率小技巧：
可以在 ~/.bashrc 中添加别名，方便日后快速激活环境：
alias a1="conda activate OmniRobCtrl"
添加后记得运行 source ~/.bashrc

#6. 注意事项：版本一致性
在配置过程中，CUDA、PyTorch 和 LibTorch 的版本一致性

显卡驱动	nvidia-smi	查看支持的最高 CUDA 版本
PyTorch	python -c "import torch; print(torch.version.cuda)"	必须与 Isaac Gym 底层编译的 CUDA 版本一致
LibTorch	(内置于 Isaac Gym)	必须与安装的 PyTorch 版本匹配

如果版本不匹配（例如 PyTorch 是基于 CUDA 11.3 编译，而 Isaac Gym 依赖 11.4），可能导致：
仿真启动瞬间崩溃 (Segmentation Fault)
模型加载失败 (RuntimeError)
训练结果完全不收敛或数值异常 (NaN)