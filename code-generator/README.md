# Code Generator ⚡

A project that uses frontier and open-source LLMs to port Python code into high-performance C++ or Rust, then compiles and benchmarks the result against the original Python.

## Overview

The core idea across all three notebooks: give an LLM a Python snippet plus details about your machine (OS, CPU, compiler toolchain), ask it to generate the fastest possible equivalent in a compiled language, then compile and run both versions to compare execution time. Speedups of 10x–1000x+ are typical since the generated code skips Python's interpreter overhead entirely.

## Files

| File | Description |
|---|---|
| `code_generation_basics.ipynb` | Introductory version — single-model (Gemini/GPT/Claude/Grok, pick one), Python → C++ porting, with a system-info check and step-by-step guidance on installing a C++ compiler if needed. Good starting point for understanding the core flow. |
| `code_generator.ipynb` | The main, most complete notebook — supports Python → **Rust** (or C++) porting, offers a dropdown of frontier and open-source models (GPT-5, Claude Sonnet 4.5, Grok 4, Gemini 2.5 Pro, Qwen, DeepSeek Coder, GPT-OSS), and wraps everything in a styled `gr.Blocks` UI with side-by-side Python/output code panels, a model selector, and convert/run buttons. |
| `open_source_code_generation.ipynb` | Same multi-model Python → C++ workflow as `code_generator.ipynb`, but focused on comparing **open-source models** (via Ollama, Groq, OpenRouter) against frontier models on both correctness and speed. |
| `styles.py` | Custom CSS (`CSS` constant) for the Gradio UI — dark theme, color-coded Python/output panels, styled convert/run buttons. |
| `system_info.py` | Helper module (`retrieve_system_info()`, `rust_toolchain_info()`) that inspects the local OS, CPU, compiler toolchain (clang/gcc/MSVC), and Rust toolchain (rustc/cargo/rustup) so the LLM can tailor compile commands and optimization flags to your machine. |
| `requirements.txt` | Python dependencies for the project environment. |

## Key Concepts Covered

- **Prompt engineering for code translation**: system prompts that constrain the model to return only compilable code
- **Multi-provider client setup**: a single `OpenAI`-compatible client pattern pointed at different base URLs (OpenAI, Anthropic, Gemini, Grok, Groq, Ollama, OpenRouter) so any model can be swapped in via a `clients` dict
- **System-aware prompting**: feeding CPU/OS/toolchain info into the prompt so the model can suggest correct compiler flags (e.g. `-march=native`, `-Ofast`, `lto=fat`)
- **Compile-and-benchmark loop**: writing generated code to `main.cpp`/`main.rs`, compiling with `subprocess`, running it, and timing it against the original Python via `exec()`
- **Gradio `Blocks` UI**: custom multi-panel layout with a model dropdown, convert button, and styled output panes (`styles.py`)

## Setup

```bash
pip install -r requirements.txt
jupyter lab
```

1. Create a `.env` file with whichever provider keys you plan to use:
All are optional except the provider(s) whose model(s) you actually select — comment out the rest.
2. Make sure a C++ compiler (clang/gcc/MSVC) and/or Rust toolchain (`rustc`/`cargo`, via `rustup`) is installed locally, matching whichever language you're targeting.
3. Start with `code_generation_basics.ipynb` for the simplest single-model flow, then move to `code_generator.ipynb` for the full multi-model Rust/C++ UI, or `open_source_code_generation.ipynb` to benchmark open-source models specifically.

## Notes

- `system_info.py` is shared across all three notebooks — it detects your OS, CPU features (SIMD flags), available compilers, and Rust toolchain so prompts can be tailored to your hardware.
- Benchmark results in the notebooks (speedup multipliers per model) are from the author's own runs and will vary by machine and model version.