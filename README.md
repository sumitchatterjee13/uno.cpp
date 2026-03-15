<p align="center">
  <img src="assets/uno-logo.png" alt="Uno.cpp Logo" width="128" height="128">
</p>

<h1 align="center">Uno.cpp</h1>

<p align="center">
  <strong>Un-official llama.cpp — run community-quantized models before they hit upstream</strong>
</p>

<p align="center">
  <a href="https://github.com/sumitchatterjee13/uno.cpp/releases">
    <img src="https://img.shields.io/github/v/release/sumitchatterjee13/uno.cpp?style=flat-square&label=download" alt="Download">
  </a>
  <a href="https://github.com/ggml-org/llama.cpp/blob/master/LICENSE">
    <img src="https://img.shields.io/badge/license-MIT-blue?style=flat-square" alt="License: MIT">
  </a>
  <a href="https://huggingface.co/Sumitc13/sarvam-30b-GGUF">
    <img src="https://img.shields.io/badge/🤗_Models-Sarvam_30B_GGUF-yellow?style=flat-square" alt="HuggingFace Models">
  </a>
</p>

---

## What is Uno.cpp?

Uno.cpp is a ready-to-use desktop application that lets you run GGUF models locally — especially models with new architectures that haven't been merged into the official [llama.cpp](https://github.com/ggml-org/llama.cpp) yet.

**The problem:** When a new model architecture is quantized to GGUF, the architecture support PR can take weeks to get reviewed and merged into llama.cpp. During this time, users have no easy way to run these models — they'd need to manually clone a fork, set up a C++ build environment, compile from source, and use the command line.

**The solution:** Uno.cpp packages a custom-built `llama-server` (with the new architecture support baked in) into a simple Windows installer with a GUI launcher. Download, install, pick your model file, chat. That's it.

## Currently Supported Models

| Model | Architecture | Parameters | HuggingFace | Upstream PR |
|-------|-------------|------------|-------------|-------------|
| [Sarvam-30B](https://huggingface.co/sarvamai/sarvam-30b) | `sarvam_moe` | ~30B (MoE) | [GGUF Downloads](https://huggingface.co/Sumitc13/sarvam-30b-GGUF) | [#20275](https://github.com/ggml-org/llama.cpp/pull/20275) |

> Sarvam-30B is an open-source Mixture-of-Experts model by [Sarvam AI](https://www.sarvam.ai/) with 128 routed experts (top-6 routing) + 1 shared expert, 262K vocabulary, and strong multilingual capabilities across Indian languages.

More models will be added as new architectures are quantized.

## Quick Start

### 1. Download and Install

Download the latest **`Unocpp-Setup-v*.exe`** from [Releases](https://github.com/sumitchatterjee13/uno.cpp/releases) and run the installer.

### 2. Download a Model

Grab a GGUF model file from HuggingFace:

| Quant | Size | VRAM Needed | Best For |
|-------|------|-------------|----------|
| [Q4_K_M](https://huggingface.co/Sumitc13/sarvam-30b-GGUF) | ~19 GB | ~20 GB | Most users with 24GB+ VRAM |
| [Q6_K](https://huggingface.co/Sumitc13/sarvam-30b-GGUF) | ~26 GB | ~27 GB | Higher quality, 32GB VRAM |
| [Q8_0](https://huggingface.co/Sumitc13/sarvam-30b-GGUF) | ~34 GB | ~35 GB | Best quantized quality |
| [BF16](https://huggingface.co/Sumitc13/sarvam-30b-GGUF) | ~64 GB | ~65 GB | Full precision, multi-GPU |

### 3. Launch and Chat

Open **Uno.cpp** from your desktop → select the `.gguf` file → your browser opens with a chat UI. Done.

## Features

- **No terminal required** — GUI launcher with model file picker and settings
- **Remembers your config** — model path, GPU layers, context size saved between sessions
- **Built-in chat UI** — powered by llama.cpp's web interface, opens in your browser
- **GPU accelerated** — CUDA support for NVIDIA GPUs, configurable layer offloading
- **Adjustable settings** — GPU layers, context size (2K–32K), and port configuration
- **Fully local & private** — no cloud, no API keys, everything runs on your machine
- **OpenAI-compatible API** — `llama-server` exposes `/v1/chat/completions` so you can connect other tools

## System Requirements

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| OS | Windows 10 64-bit | Windows 11 |
| GPU | NVIDIA with 20+ GB VRAM | RTX 3090 / 4090 / 5090 |
| CUDA | CUDA 12.x drivers | Latest NVIDIA drivers |
| RAM | 16 GB | 32 GB |
| Disk | Space for model files | SSD for faster loading |

> **No NVIDIA GPU?** Set GPU Layers to `0` in the launcher to run on CPU only (significantly slower but works).

## Building From Source

If you prefer to build from source instead of using the installer:

### Prerequisites

- Visual Studio 2022 with C++ workload
- CMake 3.21+
- NVIDIA CUDA Toolkit 12.x (for GPU support)
- Git

### Build Steps

```powershell
# Clone the repo
git clone https://github.com/sumitchatterjee13/uno.cpp.git
cd uno.cpp

# Create build directory
mkdir build && cd build

# Configure with CUDA
cmake .. -DGGML_CUDA=ON -DCMAKE_BUILD_TYPE=Release

# Build the server
cmake --build . --config Release --target llama-server
```

> **Tip:** Use the **Developer Command Prompt for VS 2022** (not regular cmd) so CMake and the compiler are in PATH.

The binary will be at `build/bin/Release/llama-server.exe`. Run it directly:

```powershell
llama-server.exe -m path/to/model.gguf -ngl 99 -c 4096
```

Then open `http://127.0.0.1:8080` in your browser.

## How It Works

Uno.cpp is a fork of [llama.cpp](https://github.com/ggml-org/llama.cpp) with added architecture support for models that haven't been merged upstream yet. The app consists of:

1. **`llama-server.exe`** — custom-built llama.cpp inference server with new architecture support
2. **A GUI launcher** — pick your model, configure GPU layers and context size, hit launch
3. **Built-in chat UI** — opens in your browser at `http://127.0.0.1:8080`

The server also exposes an OpenAI-compatible API, so you can connect tools like Open WebUI, SillyTavern, or any OpenAI-compatible client.

## FAQ

**Q: Is this a fork of llama.cpp?**
Yes. Uno.cpp is llama.cpp with additional architecture support patches. Once those patches are merged upstream, Uno.cpp will sync with the latest llama.cpp and focus on the next set of unsupported models.

**Q: Will my existing models work with this?**
If your model uses a standard llama.cpp architecture (LLaMA, Mistral, Qwen, Gemma, Phi, etc.), it will work out of the box. Uno.cpp adds support on top — it doesn't remove anything.

**Q: Is it safe to install?**
The code is fully open source. The installer bundles only `llama-server.exe`, its runtime DLLs, and launcher scripts. You can verify everything by building from source.

**Q: macOS / Linux support?**
Currently Windows only. macOS and Linux users can build from source — the llama.cpp codebase supports all platforms.

## Credits

- [llama.cpp](https://github.com/ggml-org/llama.cpp) by Georgi Gerganov and contributors — the foundation this project is built on
- [Sarvam AI](https://www.sarvam.ai/) — creators of the Sarvam-30B model

## License

This project is licensed under the [MIT License](LICENSE), same as llama.cpp.

---

<p align="center">
  <em>Built by <a href="https://github.com/sumitchatterjee13">Sumit Chatterjee</a> — because nobody should have to wait for a PR merge to run a model.</em>
</p>
