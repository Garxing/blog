# huggingface镜像源更换
主要参考了[镜像](https://zhuanlan.zhihu.com/p/678204385)这篇文章，先进入conda虚拟环境中，然后再输入下面命令，注意根据操作系统进行调整，更新镜像源后，不会干扰到其他Python环境
```bash
# linux环境下
pip install -U huggingface_hub
export HF_ENDPOINT=https://hf-mirror.com
```
```bash
# windows环境下
pip install -U huggingface_hub
set HF_ENDPOINT=https://hf-mirror.com
```