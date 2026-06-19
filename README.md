# FLUX.2 Sketch Workflows

## About this project

This repository includes ComfyUI workflows for FLUX.2-based sketch generation and sketch-guided image generation:

- `flux2_image_to_sketch_to_image.json`: converts an input image into a sketch, then uses that sketch to guide a final generated image.
- `flux2_sketch_to_image_painter.json`: lets the user draw a sketch with PainterNode inside ComfyUI, then generates an image from the drawn sketch.

The workflows are built around FLUX.2 Klein Base 4B, Qwen text encoding for FLUX.2, FLUX.2 VAE files, and sketch-related LoRAs shared by the community. Some workflows also use community custom nodes such as PainterNode.

The purpose of this repository is to provide practical, editable ComfyUI workflow examples for experimenting with sketch-to-image pipelines, reference latent conditioning, LoRA switching, and simple two-stage image transformation flows.

This project does not claim ownership of the underlying models, LoRAs, custom nodes, or third-party components. Please follow the licenses and usage terms of each upstream project and model source.

These workflows were created and tested with **ComfyUI v0.10.0**. PainterNode does not currently publish a specific minimum ComfyUI version requirement, but using a recent ComfyUI version is recommended because the workflows rely on recent built-in nodes such as `ReferenceLatent` and `EmptyFlux2LatentImage`.

## Reference Links

These links are provided as technical background and source references. They are not all required to run the workflows directly.

- YouTube: [How to Train a ControlNet in AI Toolkit](https://www.youtube.com/watch?v=CXJ95qI_9Xg)
- AI Toolkit repo, for training/reference only: [ostris/ai-toolkit](https://github.com/ostris/ai-toolkit)

## Setup Notes

- The workflow file names below are the exact names referenced by the local ComfyUI workflows.
- `ReferenceLatent` and `EmptyFlux2LatentImage` are built-in nodes in recent ComfyUI versions; they are not external custom nodes.

## Shared Model Sources

### FLUX.2 Klein Base 4B

- Hugging Face repo: [black-forest-labs/FLUX.2-klein-base-4B](https://huggingface.co/black-forest-labs/FLUX.2-klein-base-4B)
- Files page: [FLUX.2-klein-base-4B files](https://huggingface.co/black-forest-labs/FLUX.2-klein-base-4B/tree/main)
- Single-file diffusion model in repo: [flux-2-klein-base-4b.safetensors](https://huggingface.co/black-forest-labs/FLUX.2-klein-base-4B/resolve/main/flux-2-klein-base-4b.safetensors)

### ComfyUI Text Encoder and VAE Files

- Text encoder: [`qwen_3_4b.safetensors`](https://huggingface.co/Comfy-Org/z_image_turbo/tree/main/split_files/text_encoders)
- VAE: [`flux2-vae.safetensors`](https://huggingface.co/Comfy-Org/flux2-dev/tree/main/split_files/vae)

### Ostris Sketch LoRAs

- Hugging Face repo: [ostris/sketch_to_image_klein_4b](https://huggingface.co/ostris/sketch_to_image_klein_4b)
- Files page: [sketch_to_image_klein_4b files](https://huggingface.co/ostris/sketch_to_image_klein_4b/tree/main)
- Image-to-sketch LoRA: [sketch_generator_klein_4b.safetensors](https://huggingface.co/ostris/sketch_to_image_klein_4b/resolve/main/sketch_generator_klein_4b.safetensors)
- Sketch-to-image LoRA: [sketch_to_image_klein_4b.safetensors](https://huggingface.co/ostris/sketch_to_image_klein_4b/resolve/main/sketch_to_image_klein_4b.safetensors)

### PainterNode Custom Node

- GitHub repo: [AlekPet/ComfyUI_Custom_Nodes_AlekPet](https://github.com/AlekPet/ComfyUI_Custom_Nodes_AlekPet)
- PainterNode directory: [PainterNode](https://github.com/AlekPet/ComfyUI_Custom_Nodes_AlekPet/tree/master/PainterNode)
- Install path:

```text
ComfyUI/custom_nodes/ComfyUI_Custom_Nodes_AlekPet
```

## Workflow Requirements

### `flux2_image_to_sketch_to_image.json`

Purpose:

- Stage 1: input image to clean sketch.
- Stage 2: generated sketch to final image.

Required model files:

| Workflow file name | Type | Put in | Download/source |
|---|---|---|---|
| `flux-2-klein-4b.safetensors` | Diffusion model / UNet | `ComfyUI/models/diffusion_models/` | Use [black-forest-labs/FLUX.2-klein-base-4B](https://huggingface.co/black-forest-labs/FLUX.2-klein-base-4B). The official single-file name is [flux-2-klein-base-4b.safetensors](https://huggingface.co/black-forest-labs/FLUX.2-klein-base-4B/resolve/main/flux-2-klein-base-4b.safetensors). |
| `qwen_3_4b.safetensors` | Text encoder / CLIP | `ComfyUI/models/text_encoders/` | Download from [Comfy-Org/z_image_turbo text_encoders](https://huggingface.co/Comfy-Org/z_image_turbo/tree/main/split_files/text_encoders). |
| `flux2-vae.safetensors` | VAE | `ComfyUI/models/vae/` | Download from [Comfy-Org/flux2-dev vae](https://huggingface.co/Comfy-Org/flux2-dev/tree/main/split_files/vae). |
| `sketch_generator_klein_4b.safetensors` | LoRA, image-to-sketch | `ComfyUI/models/loras/` | [Download](https://huggingface.co/ostris/sketch_to_image_klein_4b/resolve/main/sketch_generator_klein_4b.safetensors) |
| `sketch_to_image_klein_4b.safetensors` | LoRA, sketch-to-image | `ComfyUI/models/loras/` | [Download](https://huggingface.co/ostris/sketch_to_image_klein_4b/resolve/main/sketch_to_image_klein_4b.safetensors) |

Also required input file:

| Workflow file name | Type | Put in | Notes |
|---|---|---|---|
| `how-to-draw-a-cartoon-tree-featured-image-1200.png` | Input image | `ComfyUI/input/` | This is referenced by the workflow's `LoadImage` node. You can replace it with another input image in ComfyUI. |

### `flux2_sketch_to_image_painter.json`

Purpose:

- Draw a sketch in `PainterNode`, then generate an image from that sketch.

Required nodes:

| Node | Source | Required external install |
|---|---|---|
| `PainterNode` | [AlekPet/ComfyUI_Custom_Nodes_AlekPet](https://github.com/AlekPet/ComfyUI_Custom_Nodes_AlekPet) | Yes |

PainterNode install command:

```powershell
cd ComfyUI/custom_nodes
git clone https://github.com/AlekPet/ComfyUI_Custom_Nodes_AlekPet.git
```

Required model files:

| Workflow file name | Type | Put in | Download/source |
|---|---|---|---|
| `flux-2-klein-4b.safetensors` | Diffusion model / UNet | `ComfyUI/models/diffusion_models/` | Use [black-forest-labs/FLUX.2-klein-base-4B](https://huggingface.co/black-forest-labs/FLUX.2-klein-base-4B). The official single-file name is [flux-2-klein-base-4b.safetensors](https://huggingface.co/black-forest-labs/FLUX.2-klein-base-4B/resolve/main/flux-2-klein-base-4b.safetensors). |
| `qwen_3_4b.safetensors` | Text encoder / CLIP | `ComfyUI/models/text_encoders/` | Download from [Comfy-Org/z_image_turbo text_encoders](https://huggingface.co/Comfy-Org/z_image_turbo/tree/main/split_files/text_encoders). |
| `flux2-vae.safetensors` | VAE | `ComfyUI/models/vae/` | Download from [Comfy-Org/flux2-dev vae](https://huggingface.co/Comfy-Org/flux2-dev/tree/main/split_files/vae). |
| `sketch_to_image_klein_4b.safetensors` | LoRA, sketch-to-image | `ComfyUI/models/loras/` | [Download](https://huggingface.co/ostris/sketch_to_image_klein_4b/resolve/main/sketch_to_image_klein_4b.safetensors) |

Optional generated/working file:

| Workflow file name | Type | Put in | Notes |
|---|---|---|---|
| `Paint_42.png` | PainterNode canvas image | `ComfyUI/input/` or generated by PainterNode | The workflow references it inside PainterNode; it can be regenerated by drawing in the node. |

## Acknowledgements

This project builds on the work of the ComfyUI community and several upstream model, node, and workflow contributors:

- [ComfyUI](https://github.com/comfyanonymous/ComfyUI), for the node-based image generation interface and built-in FLUX.2 workflow support.
- [black-forest-labs/FLUX.2-klein-base-4B](https://huggingface.co/black-forest-labs/FLUX.2-klein-base-4B), for the FLUX.2 Klein Base 4B model used by these workflows.
- [Comfy-Org](https://huggingface.co/Comfy-Org), for ComfyUI-ready model component files including the text encoder and VAE sources referenced in this README.
- [ostris/sketch_to_image_klein_4b](https://huggingface.co/ostris/sketch_to_image_klein_4b), for the sketch generation and sketch-to-image LoRAs.
- [AlekPet/ComfyUI_Custom_Nodes_AlekPet](https://github.com/AlekPet/ComfyUI_Custom_Nodes_AlekPet), for PainterNode and related custom node work.

All models, LoRAs, custom nodes, and third-party assets remain the property of their respective creators and are subject to their own licenses and usage terms.
