<p align="center">
<img width="500px" alt="Project Baize" src="https://user-images.githubusercontent.com/22514219/229195563-0cddfa74-e52f-4413-b4b4-e4ba489c4b3d.png">
</p>
<p align="center"><a href="https://arxiv.org/abs/2304.01196">[📄 Paper]</a> | <a href="https://huggingface.co/spaces/project-baize/Baize-7B">[🤗 Demo]</a> | <a href="https://github.com/project-baize/baize-chatbot#cli-and-api">[🔧 CLI & API]</a> </p>
<hr>

## News
- **[Apr. 27, 2023]** [Fastchat](https://github.com/lm-sys/FastChat) now supports Baize. Try the new [CLI and API](https://github.com/project-baize/baize-chatbot#cli-and-api)!
- **[Apr. 21, 2023]** We now have a [script](https://github.com/project-baize/baize-chatbot#merge-lora-into-llama) to merge LoRA weights into standard HF model so you can use it everywhere HF is supported!

## What's Baize?
Baize is an open-source chat model trained with [LoRA](https://github.com/microsoft/LoRA). It uses 100k dialogs generated by letting ChatGPT chat with itself. We also use Alpaca's data to improve its performance. We have released 7B, 13B and 30B models. Please refer to the [paper](https://arxiv.org/pdf/2304.01196.pdf) for more details.

## Why it's called Baize?
Baize (pronounced as By-zor; Simplified Chinese 白泽, Traditional Chinese 白澤, Japanese 白沢, はくたく) is a mythical creature in Chinese folklore, who speaks human languages and knows everything. This is exactly what we expect from a chat model.

## Overview
⚠️ All model weights and data are for **research use ONLY**. Commercial use is **strictly prohibited**. We accept **NO responsibility or liability** for any use of our data, code or weights.

This is the repo for the Baize project, which aims to build a chat model with LLaMA. This repository contains:

- 54K/57K/47K [dialogs](data) from Quora, StackOverFlow and MedQuAD questions
- The [code](collect.py) for collecting self-chat data
- The [code](finetune.py) for training Baize
- The [code](demo/app.py) for chat model demo (forked from [ChuanhuChatGPT](https://github.com/GaiZhenbiao/ChuanhuChatGPT))

### Model Release
- [Baize-7B](https://huggingface.co/project-baize/baize-lora-7B)
- [Baize-13B](https://huggingface.co/project-baize/baize-lora-13B)
- [Baize-30B](https://huggingface.co/project-baize/baize-lora-30B)
- [Baize Healthcare-7B](https://huggingface.co/project-baize/baize-healthcare-lora-7b)
- Baize Chinese-7B (Coming soon)

### Community Models and Data
- [Fauno](https://github.com/RSTLess-research/Fauno-Italian-LLM/) is an Italian version of Baize.
- [Dutch Data](https://github.com/project-baize/baize-chatbot/issues/34): Baize data translated into Dutch.

## CLI and API
Now you can use Baize with [Fastchat](https://github.com/lm-sys/FastChat) for the CLI and API provided by Fastchat!

First, install the latest version of Fastchat:
```bash
pip install git+https://github.com/huggingface/peft.git
pip install git+https://github.com/lm-sys/FastChat.git
```

Then, merge Baize's LoRA weights into LLaMA. Take 7B checkpoint as an example.
```bash
# Note you have to include "baize" in the target directory so Fastchat can recognize Baize.
python3 -m fastchat.model.apply_lora --base huggyllama/llama-7b --target ./model_weights/baize-7b --lora project-baize/baize-lora-7B
```

Now, run the CLI in your terminal! More options and configs can be found [here](https://github.com/lm-sys/FastChat#inference-with-command-line-interface).
```bash
# Optional: Add `--style rich` for better style.
python -m fastchat.serve.cli --model-path ./model_weights/baize-7b
```

You can use Baize with OpenAI API or Hugging Face API following the instruction [here](https://github.com/lm-sys/FastChat#api).

## Demo
[![Open in Spaces](https://huggingface.co/datasets/huggingface/badges/raw/main/open-in-hf-spaces-md.svg)](https://huggingface.co/spaces/project-baize/Baize-7B) 
[![Duplicate this Space](https://huggingface.co/datasets/huggingface/badges/raw/main/duplicate-this-space-md.svg)](https://huggingface.co/spaces/project-baize/Baize-7B?duplicate=true)

<p align="center">
  <img alt="Demo" src="https://user-images.githubusercontent.com/22514219/229863275-0e83c1cf-0661-4afa-9a47-1ce20fb521ae.gif">
</p>

You can either host it on your local machine or access the [online demo](https://huggingface.co/spaces/project-baize/Baize-7B). The demo fetches the [LLaMA](https://huggingface.co/huggyllama/llama-7b) model and the [LoRA weights](https://huggingface.co/project-baize/baize-lora-7B) from the Hugging Face model hub, then runs a user-friendly Gradio interface for chatting.

### How to Run Locally

First, make sure your Python version is 3.8, and then install the required packages using the command below:

```bash
cd demo
pip install -r requirements.txt
```

You can host the model on your local machine using the following command:

```bash
# We assume you have obtained access to use LLaMA. The following LLaMA weights are from a 3rd party.
base_model=huggyllama/llama-7b
lora_model=project-baize/baize-lora-7B
python app.py $base_model $lora_model
```
#### GPU VRAM Requirements
|           | Inference (without int8) |
|-----------|--------------------------|
| Baize-7B  | 16GB                     |
| Baize-13B | 28GB                     |
| Baize-30B | 67GB                     |

If you have a GPU with smaller VRAM, you can do inference with `int8`, by passing the 8bit argument:

```bash
python app.py $base_model $lora_model 8bit
```

## How to Reproduce

### Setup

1. Install dependencies

```bash
pip install -r requirements.txt
```

2. If `bitsandbytes` doesn't work, [install it from source](https://github.com/TimDettmers/bitsandbytes/blob/main/compile_from_source.md). Windows users can follow [these instructions](https://github.com/tloen/alpaca-lora/issues/17).

### Data Collecting

You can use our [released data](data) or collect the data from ChatGPT using the following command:

```bash
num_process=10 # The number of processes to collect data
max_total_tokens=500000 # Set maximum numbers of tokens to collect data
api_key=xxxxxxxxxxxxxxxxx # Set your openai api key
for ((i=0; i<$num_process; i++))
do
    python collect.py $api_key $max_total_tokens $i $num_process stackoverflow &
    python collect.py $api_key $max_total_tokens $i $num_process quora &
    python collect.py $api_key $max_total_tokens $i $num_process medical &
done
```

After collecting data, you use the following command to preprocess data:

```bash
python preprocess.py stackoverflow
python preprocess.py quora
python preprocess.py medical
```

### Use your own data

If there's a specific dataset you want to use as seeds for ChatGPT self-chatting, you can simply modify `collect.py` to load your own data. 

### Training

The fine-tuning code is designed to run on an A100-80G GPU. The `finetune.py` script accepts three parameters: foundation model size (i.e., 7B, 13B, or 30B), batch size, learning rate and datasets. Note the total batch size is fixed to 64 (can be modified [here](https://github.com/project-baize/baize/blob/cbcf39902fcdfab8d935b7ea771a4e7d452a1be0/finetune.py#L24)) and the batch size here is the per device batch size before gradient accumulation. Set it to a smaller value if you are training on a GPU with smaller VRAM.

```bash
# For the 7B model (takes about 9 hours)
python finetune.py 7b 32 0.0002 alpaca,stackoverflow,quora

# For the 13B model (takes about 16 hours)
python finetune.py 13b 16 0.0001 alpaca,stackoverflow,quora

# For the 30B model (takes about 36 hours)
python finetune.py 30b 8 0.00005 alpaca,stackoverflow,quora
```
#### GPU VRAM Consumption
With the settings ABOVE:

|           | Training (with int8) |
|-----------|----------------------|
| Baize-7B  | 26GB                 |
| Baize-13B | 25GB                 |
| Baize-30B | 42GB                 |

Got a question? See [this issue](https://github.com/project-baize/baize-chatbot/issues/26).

### Merge LoRA into LLaMA
Now you can easily merge the trained LoRA weights into a LLaMA model so you can use it with everything that supports standard Hugging Face API!

Here's an example for merging `baize-lora-7B` into LLaMA-7B.
```bash
python merge_lora.py \
--base huggyllama/llama-7b \
--target ~/model_weights/baize-7b \
--lora project-baize/baize-lora-7B
```

## Citation
```bibtex
@article{xu2023baize,
  title={Baize: An Open-Source Chat Model with Parameter-Efficient Tuning on Self-Chat Data},
  author={Xu, Canwen and Guo, Daya and Duan, Nan and McAuley, Julian},
  journal={arXiv preprint arXiv:2304.01196},
  year={2023}
}
```
<hr>

[![Share to Community](https://huggingface.co/datasets/huggingface/badges/raw/main/powered-by-huggingface-light.svg)](https://huggingface.co)
