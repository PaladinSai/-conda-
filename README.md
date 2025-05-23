# Conda 全套使用指南（适合计算机小白，Linux 命令行为主）

## 前言

Conda 是一个流行的包管理和环境管理工具，广泛用于生物信息学、数据科学等领域。它能帮助你轻松安装、管理各种软件包，并隔离不同项目的环境，避免依赖冲突。  
本指南以“小白友好”为原则，**主要以 Linux 命令行为主**，带你从零开始学会 Conda 的安装、基本操作和常见问题解决。

---

## 1. Conda 是什么？

- **包管理**：帮你下载安装、升级、卸载各种软件和库（如 Python、R 及其扩展包）。
- **环境管理**：可以创建、切换、删除“环境”，每个环境可以有不同的软件和版本，互不影响。

---

## 2. 在 Linux 下安装 Conda

推荐安装【Miniconda】（小巧，只含必要组件，也适合服务器/集群环境）。

### 2.1 下载 Miniconda 安装脚本

请先打开你的终端（Terminal），然后输入：

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
```
如果你没有 `wget` 命令，可以用 `curl`：

```bash
curl -O https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
```

### 2.2 运行安装脚本

```bash
bash Miniconda3-latest-Linux-x86_64.sh
```

- 按提示阅读许可协议（一直按回车，到末尾输入 `yes`）。
- 建议安装在默认路径（一般为 `~/miniconda3`），也可以自定义。
- 当问你“Do you wish the installer to initialize Miniconda3 by running conda init?”时，建议输入 `yes`，这样以后打开终端会自动添加 conda 环境变量。

### 2.3 激活 Conda

安装完后，执行：

```bash
source ~/.bashrc
```

如果你用的是 zsh，请执行：

```bash
source ~/.zshrc
```

### 2.4 检查 conda 是否安装成功

```bash
conda --version
```
看到版本号说明安装成功。

---

## 3. Conda 常用命令

### 3.1 更新 conda 本身

```bash
conda update conda
```
按照提示输入 y 回车即可。

### 3.2 创建环境

假如你想为某个项目创建一个 Python 3.10 环境，命名为“bio”：

```bash
conda create -n bio python=3.10
```
- `-n bio`：指定环境名为 bio
- `python=3.10`：指定 Python 版本

### 3.3 激活/切换环境

```bash
conda activate bio
```
此后所有操作都会在 bio 环境下进行。

#### 如何判断环境是否激活成功？

- 成功激活后，命令行前面会出现括号里的环境名。例如：

```bash
(bio) user@hostname:/your/path$
```

- 如果你看到命令提示行前缀从 `(base)` 变成了 `(bio)`，就说明已经切换到了 bio 环境。

- 你也可以用命令确认当前环境：

```bash
conda info --envs
```
当前激活的环境会在环境名左侧有一个星号 `*`，例如：

```
# conda environments:
#
base    /home/yourname/miniconda3
bio  *  /home/yourname/miniconda3/envs/bio
```
这里 `bio` 行左边的 `*` 表示当前激活的是 bio 环境。

### 3.4 退出当前环境（回到 base）

```bash
conda deactivate
```

### 3.5 安装软件包

比如要安装 numpy：

```bash
conda install numpy
```
要安装生物信息学软件（如 biopython）：

```bash
conda install biopython
```

#### 安装指定版本的软件包（以 numpy 为例）

有时候你需要安装某个指定版本（比如 1.21.6）的 numpy，可以这样操作：

```bash
conda install numpy=1.21.6
```

- 只需在包名后加上 `=版本号` 即可。
- 其他包也可同理，如 `conda install pandas=1.3.5`。

### 3.6 查看已安装包

```bash
conda list
```
会显示当前环境下所有已安装的包。

---

## 4. 如何检查指定包是否已安装

有时候你需要确认某个包是否已在当前环境中安装，可以用下面的方法：

### 4.1 查看单个包是否安装

```bash
conda list 包名
# 例如 conda list numpy
```
如果已安装，会显示该包的版本等信息；如果没安装，什么也不会显示。

### 4.2 检查多个包是否已安装（借助 grep 命令）

例如同时检查 numpy、pandas、scipy 是否安装：

```bash
conda list | grep -E 'numpy|pandas|scipy'
```
- `grep -E` 表示用“或”规则匹配多个包名。
- 如果有对应包，会列出信息；没有则不显示。

---

## 5. 配置 Bioconda 和 conda-forge 软件源（生物信息必备）

**只需执行下列四行命令，即可同时配置好 defaults、conda-forge 及 bioconda 仓库，无需单独添加 conda-forge！**

```bash
conda config --add channels defaults
conda config --add channels conda-forge
conda config --add channels bioconda
conda config --set channel_priority strict
```

> **顺序很重要！**  
> 必须是 defaults → conda-forge → bioconda

配置后，你可以直接用 `conda install 包名` 安装绝大多数生物信息学和科学计算常用包。

### 检查频道设置

```bash
conda config --get channels
```
显示出来的频道顺序应为 defaults、conda-forge、bioconda。

---

## 6. 环境导出与共享

- 导出环境配置（如 bio）：

```bash
conda env export > bio_env.yml
```

- 用 yml 文件恢复环境：

```bash
conda env create -f bio_env.yml
```

---

## 7. 常见问题与小技巧

### 7.1 命令行打不开/报错？

- 确认你是在终端，并且已经执行过 `source ~/.bashrc` 或 `source ~/.zshrc`。
- 如果提示 `conda: command not found`，说明 conda 没加入环境变量，建议重装或手动添加。

### 7.2 “找不到包”，怎么办？

- 加上 `-c bioconda` 或 `-c conda-forge` 试试。
- 检查网络，必要时切换镜像源（比如用清华源或中科大源，网上查“conda 清华镜像”有详细教程）。

### 7.3 不同项目需要不同 Python 版本怎么办？

- 用不同 conda 环境分别管理，每个环境可以有自己的 Python 版本和软件。

### 7.4 环境太多占空间？

- 用 `conda env list` 查看所有环境，删除不用的环境：

```bash
conda remove -n 环境名 --all
```

---

## 8. 推荐资源

- Conda 官方文档：[https://docs.conda.io/zh/latest/](https://docs.conda.io/zh/latest/)
- Bioconda 官网与搜索：[https://bioconda.github.io/](https://bioconda.github.io/)
- 常见问题速查：[https://zhuanlan.zhihu.com/p/36999678](https://zhuanlan.zhihu.com/p/36999678)
- 生信软件 conda 安装实例：[https://bioconda.github.io/](https://bioconda.github.io/)

---

## 9. 总结

Conda 是生物信息学和数据科学领域不可或缺的工具之一。只要跟着本指南操作，遇到问题多善用搜索，基本都能解决。欢迎收藏本指南，有问题也可以随时回来查！

祝科研顺利，生信不崩溃！
