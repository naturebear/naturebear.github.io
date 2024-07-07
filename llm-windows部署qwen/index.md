# LLM Windows部署Qwen


# <center>Windows本地部署Qwen2模型实现问答</center>

## 检查显卡驱动
命令行输入```nvidia-smi```检查显卡驱动版本，此处

{{< figure src="pics/检查显卡驱动.png" width="100%" title="检查显卡驱动" >}}

## 安装CUDA
> 建议显卡驱动版本 >= CUDA版本

直接访问 [PyTorch](https://pytorch.org/get-started/locally/#supported-windows-distributions) 官网，复制命令安装

{{< figure src="pics/pytorch安装CUDA.png" width="100%" title="pytorch安装CUDA" >}}

> 安装CUDA之前，先安装并启动虚拟环境
> ```bash
> conda create -n env_qwen2 python=3.10
> conda activate env_qwen2
> pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121

> 如果下载速度较慢，安装命令需要增加镜像源
> ```bash
> pip3 install torch torchvision torchaudio -i https://pypi.tsinghua.tuna.edu.cn/simple --index-url https://download.pytorch.org/whl/cu121

> 检查CUDA是否可用
> ```python
> import torch
> print(torch.__version__)
> print(torch.version.cuda)
> print(torch.cuda.is_availbale())

## 部署Qwen2-1.5B
### 下载模型
第一步，安装依赖。
```bash
# 安装依赖
conda activate env_qwen2
pip install modelscope
```
第二步，从 [ModeScope](https://modelscope.cn/models/qwen/Qwen2-1.5B/files) 下载模型文件，点击下载模型后，复制SDK下载代码到新建的py文件中。

第三步，在代码中指定模型文件下载目录，运行即可完成下载。
> ```python
> #模型下载
> from modelscope import snapshot_download
> model_dir = snapshot_download('qwen/Qwen2-1.5B', cache_dir=r'C:\Users\Desktop\LLM\Qwen\Qwen2-1.5B')

{{< figure src="pics/ModelScope下载模型文件.png" width="100%" title="ModelScope下载模型文件" >}}

### 安装依赖
```bash
conda activate env_qwen2
pip install transformers -U
pip install accelerate
```

### 加载运行
```python
from modelscope import AutoModelForCausalLM, AutoTokenizer
from modelscope import GenerationConfig

# 指定使用 CPU or GPU 加载模型
device = "cuda" 
# 指定本地模型文件目录
model_path = r"C:\Users\Desktop\LLM\Qwen\Qwen2-1.5B\qwen\Qwen2-1___5B"

# Now you do not need to add "trust_remote_code=True"
model = AutoModelForCausalLM.from_pretrained(
    model_path,
    torch_dtype="auto",
    device_map="auto"
)
tokenizer = AutoTokenizer.from_pretrained(model_path)

# Instead of using model.chat(), we directly use model.generate()
# But you need to use tokenizer.apply_chat_template() to format your inputs as shown below
prompt = f"""请为我介绍一下什么是大语言模型"""

messages = [
    {"role": "system", "content": "你是一位乐于助人的助手。"},
    {"role": "user", "content": prompt}
]
text = tokenizer.apply_chat_template(
    messages,
    tokenize=False,
    add_generation_prompt=True
)
model_inputs = tokenizer([text], return_tensors="pt").to(device)

# Directly use generate() and tokenizer.decode() to get the output.
# Use `max_new_tokens` to control the maximum output length.
generated_ids = model.generate(
    model_inputs.input_ids,
    max_new_tokens=512
)
generated_ids = [
    output_ids[len(input_ids):] for input_ids, output_ids in zip(model_inputs.input_ids, generated_ids)
]

response = tokenizer.batch_decode(generated_ids, skip_special_tokens=True)[0]

print(response)
```
