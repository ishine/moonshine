<p align="center">
  <img src="logo.png" width="192px" />
</p>

<h1 style="text-align:center;">Moonshine</h1>

[[Blog]](https://petewarden.com/2024/10/21/introducing-moonshine-the-new-state-of-the-art-for-speech-to-text/) [[Paper 1]](https://arxiv.org/abs/2410.15608) [[Paper 2]](https://arxiv.org/abs/2509.02523) [[Model Card]](https://github.com/usefulsensors/moonshine/blob/main/model-card.md) [[Podcast]](https://notebooklm.google.com/notebook/d787d6c2-7d7b-478c-b7d5-a0be4c74ae19/audio)

Moonshine is a family of speech-to-text models optimized for fast and accurate automatic speech recognition (ASR) on resource-constrained devices. It is well-suited to real-time, on-device applications like live transcription and voice command recognition. English Moonshine obtains word-error rates (WER) better than similarly-sized tiny.en and base.en Whisper models from OpenAI, and non-English Moonshine variants [outperform](#supported-languages) Whisper Small and Medium, which are 9x and 28x larger, respectively.

English Moonshine Tiny and Base are on the [OpenASR leaderboard](https://huggingface.co/spaces/hf-audio/open_asr_leaderboard) maintained by HuggingFace:

<table>
<tr><th>Tiny</th><th>Base</th></tr>
<tr><td>

| WER        | Moonshine | Whisper |
| ---------- | --------- | ------- |
| Average    | **12.66** | 12.81   |
| AMI        | 22.77     | 24.24   |
| Earnings22 | 21.25     | 19.12   |
| Gigaspeech | 14.41     | 14.08   |
| LS Clean   | 4.52      | 5.66    |
| LS Other   | 11.71     | 15.45   |
| SPGISpeech | 7.70      | 5.93    |
| Tedlium    | 5.64      | 5.97    |
| Voxpopuli  | 13.27     | 12.00   |

</td><td>

| WER        | Moonshine | Whisper |
| ---------- | --------- | ------- |
| Average    | **10.07** | 10.32   |
| AMI        | 17.79     | 21.13   |
| Earnings22 | 17.65     | 15.09   |
| Gigaspeech | 12.19     | 12.83   |
| LS Clean   | 3.23      | 4.25    |
| LS Other   | 8.18      | 10.35   |
| SPGISpeech | 5.46      | 4.26    |
| Tedlium    | 5.22      | 4.87    |
| Voxpopuli  | 10.81     | 9.76    |

</td></tr> </table>

Moonshine's compute requirements scale with the length of input audio. This means that shorter input audio is processed faster, unlike Whisper models that process everything as 30-second chunks. To give you an idea of the benefits: Moonshine processes 10-second audio segments _5x faster_ than Whisper while maintaining the same (or significantly better!) WER/CER.

Unquantized Moonshine Base is 62M parameters (or 400MB), while Tiny is 27M parameters (around 190MB).

## Supported Languages

Moonshine also currently supports 7 additional languages. These models are more accurate than Whisper Small and Whisper Medium, while being 9x and 28x smaller, respectively, and run significantly faster on edge devices. Below is a performance summary. Arabic, Chinese, Japanese, and Korean are character-error rates (CER); all others are WER.

| Language    | Tag  | Moonshine Tiny (27M) | Moonshine Base (62M) | Whisper Tiny (39M)  | Whisper Base (74M) | Whisper Small (244M) | Whisper Medium (769M) |
| ----------  | ---- | ---------      | -------        | -------      | -------      | -------       | -------        |
| Arabic      | `ar` | 24.76          |                | 52.40        | 48.25        | 32.44         | 25.44          |
| Chinese     | `zh` | 32.77          |                | 68.51        | 59.13        | 46.76         | 40.41          |
| Japanese    | `ja` | 15.69          |                | 96.71        | 72.69        | 40.94         | 27.88          |
| Korean      | `ko` | 9.85           |                | 23.92        | 15.93        | 9.87          | 7.68           |
| Spanish     | `es` |                | TBA            |              |              |               |                |
| Ukrainian   | `uk` | 19.70          |                | 66.77        | 48.56        | 25.93         | 16.51          |
| Vietnamese  | `vi` | 15.92          |                | 96.4         | 52.79        | 26.46         | 18.49          |

Read [the paper](https://arxiv.org/abs/2509.02523) for more details on our other "flavors" of Moonshine.

## Table of Contents

- [Installation](#installation)
  - [1. Create a virtual environment](#1-create-a-virtual-environment)
  - [2a. Install the `useful-moonshine` package to use Moonshine with Torch, TensorFlow, or JAX](#2a-install-the-useful-moonshine-package-to-use-moonshine-with-torch-tensorflow-or-jax)
  - [2b. Install the `useful-moonshine-onnx` package to use Moonshine with ONNX](#2b-install-the-useful-moonshine-onnx-package-to-use-moonshine-with-onnx)
  - [3. Try it out](#3-try-it-out)
- [Examples](#examples)
  - [Live Captions](#live-captions)
  - [CTranslate2](#ctranslate2)
  - [HuggingFace Transformers](#huggingface-transformers)
  - [Web Applications](#web-applications)
- [License](#license)
- [Citation](#citation)

## Installation

We currently offer two options for installing Moonshine:

1. `useful-moonshine`, which uses Keras (with support for Torch, TensorFlow, and JAX backends)
2. `useful-moonshine-onnx`, which uses the ONNX runtime

These instructions apply to both options; follow along to get started.

Note: We like `uv` for managing Python environments, so we use it here. If you don't want to use it, simply skip the `uv` installation and leave `uv` off of your shell commands.

### 1. Create a virtual environment

First, [install](https://github.com/astral-sh/uv) `uv` for Python environment management.

Then create and activate a virtual environment:

```shell
uv venv env_moonshine
source env_moonshine/bin/activate
```

### 2a. Install the `useful-moonshine` package to use Moonshine with Torch, TensorFlow, or JAX

The `useful-moonshine` inference code is written in Keras and can run with each of the backends that Keras supports: Torch, TensorFlow, and JAX. The backend you choose will determine which flavor of the `useful-moonshine` package to install. If you're just getting started, we suggest installing the (default) Torch backend:

```shell
uv pip install useful-moonshine@git+https://github.com/usefulsensors/moonshine.git
```

To run the provided inference code, you have to instruct Keras to use the PyTorch backend by setting an environment variable:

```shell
export KERAS_BACKEND=torch
```

To run with the TensorFlow backend, run the following to install Moonshine and set the environment variable:

```shell
uv pip install useful-moonshine[tensorflow]@git+https://github.com/usefulsensors/moonshine.git
export KERAS_BACKEND=tensorflow
```

  To run with the JAX backend, run the following:

```shell
uv pip install useful-moonshine[jax]@git+https://github.com/usefulsensors/moonshine.git
export KERAS_BACKEND=jax
# Use useful-moonshine[jax-cuda] for jax on GPU
```

### 2b. Install the `useful-moonshine-onnx` package to use Moonshine with ONNX

Using Moonshine with the ONNX runtime is preferable if you want to run the models on SBCs like the Raspberry Pi. We've prepared a separate version of
the package with minimal dependencies to support these use cases. To use it, run the following:

```shell
uv pip install useful-moonshine-onnx@git+https://git@github.com/usefulsensors/moonshine.git#subdirectory=moonshine-onnx
```

### 3. Try it out

You can test whichever type of Moonshine you installed by transcribing the provided example audio file with the `.transcribe` function:

```shell
python
>>> import moonshine # or import moonshine_onnx
>>> moonshine.transcribe(moonshine.ASSETS_DIR / 'beckett.wav', 'moonshine/tiny') # or moonshine_onnx.transcribe(...)
['Ever tried ever failed, no matter try again, fail again, fail better.']
```

The first argument is a path to an audio file and the second is the name of a Moonshine model. `moonshine/tiny` and `moonshine/base` are the currently available models. If you wish to use one of the non-English Moonshine models, just append the language [IETF tag](https://en.wikipedia.org/wiki/IETF_language_tag) to the model name, e.g., `moonshine/tiny-ko`. See [the table](#supported-languages) for supported languages and their tags.

## Examples

Since the Moonshine models can be used with a variety of different runtimes and applications, we've included code samples showing how to use them in different situations. The [`demo`](/demo/) folder in this repository also has more information on many of them.

### Live Captions

You can try the Moonshine ONNX models with live input from a microphone with the [live captions demo](/demo/README.md#demo-live-captioning-from-microphone-input).

### CTranslate2

The files for the CTranslate2 versions of Moonshine are available at [huggingface.co/UsefulSensors/moonshine/tree/main/ctranslate2](https://huggingface.co/UsefulSensors/moonshine/tree/main/ctranslate2), but they require [a pull request to be merged](https://github.com/OpenNMT/CTranslate2/pull/1808) before they can be used with the mainline version of the framework. Until then, you should be able to try them with [our branch](https://github.com/njeffrie/CTranslate2/tree/master), with [this example script](https://github.com/OpenNMT/CTranslate2/pull/1808#issuecomment-2439725339).

### HuggingFace Transformers

Moonshine models are also available on the HuggingFace hub and can be used with the `transformers` library, as follows:

```python
import torch
from transformers import AutoProcessor, MoonshineForConditionalGeneration
from datasets import load_dataset

processor = AutoProcessor.from_pretrained("UsefulSensors/moonshine-tiny")
model = MoonshineForConditionalGeneration.from_pretrained("UsefulSensors/moonshine-tiny")

ds = load_dataset("hf-internal-testing/librispeech_asr_dummy", "clean", split="validation")
audio_array = ds[0]["audio"]["array"]

inputs = processor(audio_array, return_tensors="pt")

generated_ids = model.generate(**inputs)

transcription = processor.batch_decode(generated_ids, skip_special_tokens=True)[0]
print(transcription)
```

If you wish to use one of the non-English Moonshine models, just append the [IETF code](https://en.wikipedia.org/wiki/IETF_language_tag) to the repo ID, e.g., `UsefulSensors/moonshine-tiny-ko`. See [the table](#supported-languages) for supported languages and their tags.

### Web Applications

Use our [MoonshineJS](https://github.com/moonshine-ai/moonshine-js) library to run Moonshine models in the web browser with a few lines of Javascript.

## Known Issues

### UserWarning: You are using a softmax over axis 3 of a tensor of shape torch.Size([1, 8, 1, 1])
This is a benign warning arising from Keras. For the first token in the decoding loop, the attention score matrix's shape is 1x1, which triggers this warning. You can safely ignore it, or run with `python -W ignore` to suppress the warning.

## License
All inference code in this repo is released under the MIT license. The English Moonshine models are also released under the MIT license. 

All non-English Moonshine variants are released under the [Moonshine AI Community License](https://www.moonshine.ai/moonshine_community_license.txt) (TLDR: Models are free to use for researchers, developers, small businesses, and creators with less than $1M in annual revenue.). 

A copy of both licenses is included in this repository.

## Citation
If you benefit from our work, please cite our paper:
```
@misc{jeffries2024moonshinespeechrecognitionlive,
      title={Moonshine: Speech Recognition for Live Transcription and Voice Commands}, 
      author={Nat Jeffries and Evan King and Manjunath Kudlur and Guy Nicholson and James Wang and Pete Warden},
      year={2024},
      eprint={2410.15608},
      archivePrefix={arXiv},
      primaryClass={cs.SD},
      url={https://arxiv.org/abs/2410.15608}, 
}
```

Please also cite our paper on non-English Moonshine variants if you find them useful:
```
@misc{king2025flavorsmoonshinetinyspecialized,
      title={Flavors of Moonshine: Tiny Specialized ASR Models for Edge Devices}, 
      author={Evan King and Adam Sabra and Manjunath Kudlur and James Wang and Pete Warden},
      year={2025},
      eprint={2509.02523},
      archivePrefix={arXiv},
      primaryClass={cs.CL},
      url={https://arxiv.org/abs/2509.02523}, 
}
```
