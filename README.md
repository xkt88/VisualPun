# Visual Puns from Idioms: An Iterative LLM–T2IM–MLLM Framework

This repository accompanies the paper “Visual Puns from Idioms: An Iterative LLM–T2IM–MLLM Framework.” It implements the iterative loop, releases the 1,000‑idiom image set, and provides a 50‑idiom, 6‑T2IM case study. The pipeline iterates prompt → generate → infer → refine and stops on a correct recognition or after 5 steps [[1]].

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
   The paper fixes the T2IM to Qwen‑Image (1024×1024) to isolate LLM/MLLM effects [[1]].
3) Run `1 Iterative Pipeline.ipynb` to (re)generate images.
   - Choose idioms, backends, and `max_iterations` (default 5) [[1]].
   - Outputs are saved under `img/`.
4) Run `2 Case Study.ipynb` to reproduce the 50‑idiom, 6‑T2IM comparison.
   - Results are written to `case study/`.

Tips
- Create a virtual environment and install the imports listed at the top of each notebook.
- Store provider API keys as environment variables; the notebooks indicate where to read them.

## Dataset
- 1,000 English idioms → 1,000 visual‑pun images with paired prompts [[1]].  
- Idiom metadata JSON is under `Idioms/`.  
- Case‑study subset (50 idioms) with prompts/images lives in `case study/`.

## Method summary (paper)
- Given an idiom, the system: (i) composes a detailed visual prompt, (ii) synthesizes an image, (iii) infers the idiom from the image, and (iv) refines the prompt; it stops on a match or after 5 iterations [[1]].  
- Images are generated at 1024×1024 with a fixed T2IM (Qwen‑Image) in all experiments to focus on LLM/MLLM effects [[1]].  
- The case study shows multiple modern T2IMs map to the same target idiom under identical, detailed prompts; thus T2IM choice is secondary under these conditions [[1]].

## Results highlights (paper)
- MLLM choice is the primary driver of recognition accuracy; GPT MLLM is strongest overall, followed by Gemini, while the best open‑source MLLM (Gemma) is competitive with some closed models [[1]].  
- For prompt generation, Claude achieves the best average performance across MLLM partners [[1]].  
- Iterative refinement helps most within 2–3 rounds, with diminishing returns thereafter [[1]].

## Citation
If you use this repository, please cite the paper:

Kelaiti Xiao, Liang Yang, Dongyu Zhang, Paerhati Tulajiang, and Hongfei Lin. “Visual Puns from Idioms: An Iterative LLM–T2IM–MLLM Framework.” ICASSP 2025. [[1]]

---

Contact: please open a GitHub issue for questions or troubleshooting.

[1]: ICASSP (28).pdf — paper text included in this repository.






case study Images

![WPS拼图0(1)(1)](https://github.com/user-attachments/assets/800f6a82-efd6-4667-8203-3b84c8dd680f)![WPS拼图1(1)(1)](https://github.com/user-attachments/assets/f3f74cc9-bf63-4784-a4d0-4406ed811523)
