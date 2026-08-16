# Persoanlized and Styled AI Aritist

A Python pipeline to fine-tune Stable Diffusion with LoRA (Low-Rank Adaptation) and generate consistent, personalized, and stylized images.

This repository uses Hugging Face Diffusers + a lightweight in-repo LoRA injection implementation to:
- Inject LoRA layers into the UNet (see source_codes/lora_creation.py)
- Train only the LoRA parameters while freezing the base model (see main.py / source_codes/train.py)
- Save and load LoRA-only weights to apply a learned style/personalization at inference time

Repository layout (relevant files)
- main.py — training entry point (loads configurations/all_configs.yaml)
- use_model.py — minimal generation example (creates an image and saves to output_images/)
- requirements.txt — Python dependencies
- configurations/all_configs.yaml — example configuration (see below)
- source_codes/* — core implementation (config_loader, load_diffusion, lora_creation, image dataset and trainer)
- model_save/ — where LoRA weights are saved by training
- artist_images/ — place your training images here (instance images)
- output_images/ — generated outputs (created by scripts)

Quick links
- Training entry: python main.py
- Simple generation: python use_model.py
- Example generator: examples/generate_example.py

Prerequisites
- Python 3.8+
- Recommended: CUDA-capable GPU and matching PyTorch/CUDA build
- A Hugging Face access token if you use private or gated models (set HF_HOME or export HUGGINGFACE_HUB_TOKEN)

Install
1. Clone the repository:
   git clone https://github.com/ACEMAKER23/Persoanlized-and-Styled-AI-Aritist.git
   cd Persoanlized-and-Styled-AI-Aritist

2. Create and activate a virtualenv:
   python -m venv .venv
   source .venv/bin/activate   # macOS / Linux
   .venv\Scripts\activate      # Windows (PowerShell)

3. Install dependencies:
   pip install -r requirements.txt

Configuration
The repository expects a YAML config at configurations/all_configs.yaml by default. An example configuration is provided in configurations/all_configs.yaml (see file in this repo). The important keys your code references are:
- model.base_model — Hugging Face model repo id used by StableDiffusionPipeline
- model.dtype — one of "fp16" or "fp32"
- model.device — "cuda" or "cpu"
- paths.dataset_dir — path with instance images (used by ImagePromptDataset)
- LORA_CFG — LoRA hyperparameters (rank, alpha, target_modules)
- training.batch_size — batch size used by the dataloader

Training
Run training with:
python main.py

What main.py does (high level)
- Loads configurations/all_configs.yaml via source_codes/config_loader.load_config
- Loads a Stable Diffusion pipeline via source_codes/load_diffusion.load_stable_diffusion_pipeline
- Freezes the base model (VAE, text encoder, UNet base)
- Injects LoRA into the UNet using source_codes/lora_creation.inject_lora_unet
- Creates ImagePromptDataset and DataLoader
- Starts LoRATrainer (source_codes/train.LoRATrainer) to train only the LoRA parameters
- Saves LoRA weights under model_save/

Inference / Generating images
- use_model.py demonstrates a minimal generation call (see use_model.py). It will run the pipeline and save an image to output_images/final_test.png.
- To apply a saved LoRA, inject the LoRA UNet (inject_lora_unet) and load weights with load_lora_weights (source_codes/lora_creation.py). Example code is included in examples/generate_example.py.

Example usage
- Quick generate (no LoRA): python use_model.py
- Generate with LoRA weights:
  - Ensure you have trained and saved weights under model_save/<checkpoint>/
  - Update path in examples/generate_example.py or use the CLI snippet in that file
  - Run python examples/generate_example.py

Security, privacy, and dataset notes
- Do not commit private images, API keys, or large model files. Add them to .gitignore or store them externally.
- Fine-tuning on a small set of images can overfit; validate on held-out prompts.
- Respect dataset and model licenses and the people represented in datasets.

License
- Add a LICENSE file to the repo to indicate the license you want. The README previously included an MIT badge; if you want MIT, add an MIT LICENSE file.

Contact
Maintainer: haochen(ACEMAKER23)
Repository: https://github.com/ACEMAKER23/Persoanlized-and-Styled-AI-Aritist
