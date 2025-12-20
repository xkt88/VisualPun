# Visual Puns from Idioms: An Iterative LLM–T2IM–MLLM Framework

This repository accompanies the paper "Visual Puns from Idioms: An Iterative LLM–T2IM–MLLM Framework." It implements the iterative loop, releases the 1,000‑idiom image set, and provides a 50‑idiom, 6‑T2IM case study. The pipeline iterates prompt → generate → infer → refine and stops on a correct recognition or after 5 steps.

## Repository structure
- `1 Iterative Pipeline.ipynb` — end‑to‑end notebook that generates images from idioms via the iterative LLM → T2IM → MLLM loop.  
- `2 Case Study.ipynb` — reproduces the case‑study comparisons across multiple T2IMs on a curated idiom subset.  
- `img/` — 1,000 images generated from 1,000 idioms (one per idiom in the final set).  
- `Idioms/` — JSON with idioms and their corresponding descriptions.  
- `case study/` — outputs for the 50‑idiom case study spanning 6 T2IMs (images and prompts).

## Quick start
1) Open in Jupyter (Notebook/Lab) or Colab.  
2) Provide API access to:
   - an LLM (prompt generation),
   - a T2IM (image synthesis),
   - an MLLM (idiom recognition/feedback).
   The paper fixes the T2IM to Qwen‑Image (1024×1024) to isolate LLM/MLLM effects.
3) Run `1 Iterative Pipeline.ipynb` to (re)generate images.
   - Choose idioms, backends, and `max_iterations` (default 5).
   - Outputs are saved under `img/`.
4) Run `2 Case Study.ipynb` to reproduce the 50‑idiom, 6‑T2IM comparison.
   - Results are written to `case study/`.

Tips
- Create a virtual environment and install the imports listed at the top of each notebook.
- Store provider API keys as environment variables; the notebooks indicate where to read them.

## Dataset
- 1,000 English idioms → 1,000 visual‑pun images with paired prompts.  
- Idiom metadata JSON is under `Idioms/`.  
- Case‑study subset (50 idioms) with prompts/images lives in `case study/`.

## Method summary (paper)
- Given an idiom, the system: (i) composes a detailed visual prompt, (ii) synthesizes an image, (iii) infers the idiom from the image, and (iv) refines the prompt; it stops on a match or after 5 iterations.  
- Images are generated at 1024×1024 with a fixed T2IM (Qwen‑Image) in all experiments to focus on LLM/MLLM effects.  
- The case study shows multiple modern T2IMs map to the same target idiom under identical, detailed prompts; thus T2IM choice is secondary under these conditions.

## Results highlights (paper)
- MLLM choice is the primary driver of recognition accuracy; GPT MLLM is strongest overall, followed by Gemini, while the best open‑source MLLM (Gemma) is competitive with some closed models.  
- For prompt generation, Claude achieves the best average performance across MLLM partners.  
- Iterative refinement helps most within 2–3 rounds, with diminishing returns thereafter.

## Prompt template

The pipeline uses four prompt templates corresponding to the modules in Figure 2.

### 1. Prompt Generation (LLM)

> You are a creative visual designer specializing in visual puns. Given an idiom, generate a detailed text-to-image prompt that visually represents BOTH the literal and figurative meanings simultaneously.
>
> Idiom: {idiom}  
> Previous prompt: {previous_prompt or "None"}  
> Revision suggestions: {update_suggestions or "None"}
>
> Requirements:
> - Describe concrete visual elements (objects, characters, scene, composition)
> - The literal meaning should be visually depicted (e.g., actual butterflies for "butterflies in my stomach")
> - The figurative meaning should be conveyed through context, expressions, or situation
> - Include style cues (e.g., cartoon, realistic), colors, and spatial arrangement
> - Keep the prompt under 100 words
>
> Output only the image prompt, no explanation.

### 2. Idiom Inference (MLLM)

> Look at this image carefully. It represents a visual pun based on an English idiom.
>
> What idiom does this image represent? Consider both the literal visual elements and any figurative or metaphorical meaning they might convey together.
>
> Respond with only the idiom (e.g., "break the ice"), no explanation.

### 3. Semantic Equivalence Judgment (LLM)

> Determine if two idiom expressions are semantically equivalent (i.e., the same idiom in different surface forms).
>
> Inferred idiom: {inferred_idiom}  
> Target idiom: {target_idiom}
>
> Normalization rules:
> - Ignore articles (a/an/the)
> - Ignore minor verb tense differences
> - Treat singular/plural variants as equivalent
> - Focus on core meaning, not exact wording
>
> Respond with only "true" or "false".

### 4. Update Suggestions (MLLM)

> The generated image was intended to depict the idiom "{target_idiom}", but it was recognized as "{inferred_idiom}".
>
> Analyze the image and provide specific revision suggestions to make the target idiom more recognizable.
>
> Focus on:
> - Missing objects or elements essential to the idiom
> - Composition or emphasis issues
> - Ambiguous visual cues that led to misrecognition
>
> Provide 2–4 concise, actionable edits.

## Cost

### API Providers

| Model Type | Provider | Access Method |
|------------|----------|---------------|
| Closed-source (M)LLMs | OpenAI, Google, Anthropic, xAI, ByteDance | Poe API |
| Open-source (M)LLMs | Meta, DeepSeek, Z.ai, Alibaba, Mistral | DeepInfra API |
| T2IM (Qwen-Image) | Alibaba | Poe API |

### Per-Iteration Cost Estimates (USD)

| Component | Avg. Input Tokens | Avg. Output Tokens | Cost per Call |
|-----------|-------------------|-------------------|---------------|
| LLM (prompt generation) | ~150 | ~80 | ~$0.002 |
| T2IM (1024×1024) | — | — | ~$0.04 |
| MLLM (inference) | ~100 + image | ~20 | ~$0.005 |
| LLM (evaluation) | ~50 | ~5 | ~$0.0005 |
| MLLM (update) | ~150 + image | ~60 | ~$0.008 |


### Runtime

- Average time per idiom (single LLM–MLLM pair): ~25 seconds  
- Full 1,000-idiom run (one configuration): ~7 hours  
- Main experiment (100 configurations): parallelized across multiple API keys

*Note: Costs are estimates based on API pricing as of August 2025 and may vary with provider rate changes.*

![WPS拼图0(1)(1)](https://github.com/user-attachments/assets/800f6a82-efd6-4667-8203-3b84c8dd680f)
![WPS拼图1(1)(1)](https://github.com/user-attachments/assets/f3f74cc9-bf63-4784-a4d0-4406ed811523)
