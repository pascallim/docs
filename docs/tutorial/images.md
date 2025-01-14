---
sidebar_position: 3
title: "Image Generation"
---

# Image Generation

Open WebUI supports image generation through three backends: **AUTOMATIC1111**, **ComfyUI**, and **OpenAI DALL·E**. This guide will help you set up and use either of these options.

## AUTOMATIC1111

Open WebUI supports image generation through the **AUTOMATIC1111** [API](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/API). Here are the steps to get started:

### Initial Setup

1. Ensure that you have [AUTOMATIC1111](https://github.com/AUTOMATIC1111/stable-diffusion-webui) installed.
2. Launch AUTOMATIC1111 with additional flags to enable API access:
   ```
   ./webui.sh --api --listen
   ```
3. For Docker installation of WebUI with the environment variables preset, use the following command:
   ```
   docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -e AUTOMATIC1111_BASE_URL=http://host.docker.internal:7860/ -e ENABLE_IMAGE_GENERATION=True -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
   ```

### Configuring Open WebUI

1. In Open WebUI, navigate to the **Admin Panel** > **Settings** > **Images** menu.
2. Set the `Image Generation Engine` field to `Default (Automatic1111)`.
3. In the API URL field, enter the address where AUTOMATIC1111's API is accessible:
   ```
   http://<your_automatic1111_address>:7860/
   ```
   If you're running a Docker installation of Open WebUI and AUTOMATIC1111 on the same host, use `http://host.docker.internal:7860/` as your address.

## ComfyUI

ComfyUI provides an alternative interface for managing and interacting with image generation models. Learn more or download it from its [GitHub page](https://github.com/comfyanonymous/ComfyUI). Below are the setup instructions to get ComfyUI running alongside your other tools.

### Initial Setup

1. Download and extract the ComfyUI software package from [GitHub](https://github.com/comfyanonymous/ComfyUI) to your desired directory.
2. To start ComfyUI, run the following command:
   ```
   python main.py
   ```
   For systems with low VRAM, launch ComfyUI with additional flags to reduce memory usage:
   ```
   python main.py --lowvram
   ```
3. For Docker installation of WebUI with the environment variables preset, use the following command:
   ```
   docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -e COMFYUI_BASE_URL=http://host.docker.internal:7860/ -e ENABLE_IMAGE_GENERATION=True -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
   ```

### Setting Up Open WebUI with ComfyUI

#### Setting Up Flux.1 Models:

1. **Model Checkpoints**:
	* Download the Flux model(s) from the [black-forest-labs HuggingFace page](https://huggingface.co/black-forest-labs).
	* Place the model checkpoint(s) in both the `models/checkpoints` and `models/unet` directories of ComfyUI.
2. **CLIP Model**:
	* Download `clip_l.safetensors` from [here](https://huggingface.co/comfyanonymous/flux_text_encoders/tree/main).
	* Place it in the `models/clip` ComfyUI directory.
3. **T5XXL Model** (optional):
	* Download `t5xxl_fp16.safetensors` or `t5xxl_fp8_e4m3fn.safetensors` from [here](https://huggingface.co/comfyanonymous/flux_text_encoders/tree/main)
	* Place it in the `models/clip` ComfyUI directory.
4. **AE Model**:
	* Download `ae.safetensors` from [here](https://huggingface.co/black-forest-labs/FLUX.1-schnell/blob/main/ae.safetensors).
	* Place it in the `models/vae` ComfyUI directory.

To integrate ComfyUI into Open WebUI, follow these steps:

#### Step 1: Configure Open WebUI Settings

1. Navigate to the **Admin Panel** in Open WebUI.
2. Click on **Settings** and then select the **Images** tab.
3. In the `Image Generation Engine` field, choose `ComfyUI`.
4. In the **API URL** field, enter the address where ComfyUI's API is accessible, following this format: `http://<your_comfyui_address>:8188/`. 
   - Set the environment variable `COMFYUI_BASE_URL` to this address to ensure it persists within the WebUI.

#### Step 2: Verify the Connection and Enable Image Generation

1. Ensure ComfyUI is running and verify the connection to Open WebUI. You won't be able to proceed without a successful connection.
2. Once the connection is verified, toggle on **Image Generation (Experimental)**. More options will be presented to you.
3. Continue to step 3 for the final configuration steps.

#### Step 3: Configure ComfyUI Settings and Import Workflow

1. Enable developer mode within ComfyUI.
2. Export the desired workflow from ComfyUI in `API format`. The file will be downloaded as `workflow_api.json` if done correctly.
3. Return to Open WebUI and click the **Click here to upload a workflow.json file** button.
4. Select the `workflow_api.json` file to import.
5. After importing the workflow, you must map the `ComfyUI Workflow Nodes` according to the imported workflow node IDs. Note that some workflows, such as ones that use any of the Flux models, may utilize multiple nodes IDs that is necessary to fill in for their their node entry fields within Open WebUI. If a node entry field requires multiple IDs, the node IDs should be comma separated (e.g. `1` or `1, 2`).
7. Click `Save` to apply the settings and enjoy image generation with ComfyUI integrated into Open WebUI!

After completing these steps, your ComfyUI setup should be integrated with Open WebUI, and you can use the Flux.1 models for image generation.

## OpenAI DALL·E

Open WebUI also supports image generation through the **OpenAI DALL·E APIs**. This option includes a selector for choosing between DALL·E 2 and DALL·E 3, each supporting different image sizes.

### Initial Setup

1. Obtain an [API key](https://platform.openai.com/api-keys) from OpenAI.

### Configuring Open WebUI

1. In Open WebUI, navigate to the **Admin Panel** > **Settings** > **Images** menu.
2. Set the `Image Generation Engine` field to `Open AI (Dall-E)`.
3. Enter your OpenAI API key.
4. Choose the DALL·E model you wish to use. Note that image size options will depend on the selected model:
   - **DALL·E 2**: Supports `256x256`, `512x512`, or `1024x1024` images.
   - **DALL·E 3**: Supports `1024x1024`, `1792x1024`, or `1024x1792` images.

### Azure OpenAI

Using Azure OpenAI Dall-E directly is unsupported, but you can [set up a LiteLLM proxy](https://litellm.vercel.app/docs/image_generation) which is compatible with the `Open AI (Dall-E)` Image Generation Engine.

## Using Image Generation

![Image Generation Tutorial](/img/tutorial_image_generation.png)

1. First, use a text generation model to write a prompt for image generation.
2. After the response has finished, you can click the Picture icon to generate an image.
3. After the image has finished generating, it will be returned automatically in chat.

:::tip

    You can also edit the LLM's response and enter your image generation prompt as the message
    to send off for image generation instead of using the actual response provided by the
    LLM.

:::
