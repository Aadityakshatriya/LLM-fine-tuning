# LLM-fine-tuning

LoRA (PEFT) fine-tuning for **OpenAI Whisper Small** on a **local Andhra Pradesh Hindi dialect** dataset (~**13.3 hours** of transcribed Hindi audio), plus model export artifacts (merged + CTranslate2)

If you want to see this model being used in a live transcription project, please go to the **whisperlivekit-fastapi** repo:
- https://github.com/Aadityakshatriya/whisperlivekit-fastapi

## Dataset summary

- **Total audio:** ~**13.3 hours** of transcribed Hindi audio (see scan output in [whisper-small-training/wav_and_json_to_parquet.ipynb](whisper-small-training/wav_and_json_to_parquet.ipynb))
- **Districts covered (Andhra Pradesh):** Anantpur, Annamaya, Chittoor, Guntur, Krishna, SriSatyaSai, Srikakulam, Vishakapattanam (see [whisper-small-training/Vaani_IISC_dataset_download_onto_cache.ipynb](whisper-small-training/Vaani_IISC_dataset_download_onto_cache.ipynb))
- **Text cleanup / normalization:** light editing was applied to reduce transcription noise and remove English tokens/characters (see `normalize_hindi(...)` in [whisper-small-training/whisper_training.ipynb](whisper-small-training/whisper_training.ipynb))

## LoRA fine-tuning setup (key params)

Source of truth: [whisper-small-training/whisper_training.ipynb](whisper-small-training/whisper_training.ipynb)

### Base model + task
- Base model: `openai/whisper-small`
- Language: `hindi`
- Task: `transcribe`

### LoRA (PEFT) configuration
- `r = 32`
- `lora_alpha = 8`
- `target_modules = ["q_proj", "k_proj", "v_proj"]`
- `lora_dropout = 0.1`
- `bias = "none"`
- `inference_mode = False`

(Notebook printout shows ~**5,308,416** trainable params out of **247,043,328** total, i.e. ~**2.15%** trainable.)

### Training hyperparameters (trainer args)
- `per_device_train_batch_size = 4`
- `per_device_eval_batch_size = 1`
- `gradient_accumulation_steps = 8`
- `learning_rate = 2e-4`
- `warmup_steps = 100`
- `max_steps = 12000`
- `eval_steps = 400`
- `save_steps = 400`
- `logging_steps = 100`
- `save_total_limit = 20`
- `fp16 = True`
- `predict_with_generate = True`
- `generation_max_length = 225`
- `load_best_model_at_end = True`
- `metric_for_best_model = "wer"`
- `greater_is_better = False`
- `remove_unused_columns = False`
- `label_names = ["labels"]`
- `max_grad_norm = 1.0`
- `report_to = "none"`

## Repo contents (high level)

### Training / LoRA fine-tuning
- Training notebook (source of truth): [whisper-small-training/whisper_training.ipynb](whisper-small-training/whisper_training.ipynb)
- LoRA adapter checkpoints / outputs: [whisper-small-lora-ap-3/](whisper-small-lora-ap-3/) (PEFT / LoRA)

### Post-training artifacts
- Merged model outputs: [whisper-small-merged-final/](whisper-small-merged-final/) (see merge notebook: [whisper-small-training/merged_model.ipynb](whisper-small-training/merged_model.ipynb))


## Final CT2 model
- Final CT2 model folder: [whisper-small-ct2-final/](whisper-small-ct2-final/)
