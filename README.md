# Utilizing L2M3 Across Various Domains

L2M3 is not limited to extracting information related to metal-organic frameworks (MOFs) but can be applied to a wide range of fields. To adapt L2M3 for other domains, only a few modifications to the existing code are required. These modifications are explained below.

---

## 1) Common Modifications

### 1-1) LLM Model Selection
In the configuration, the first step is to decide which LLM model to use for each task. This is done in `config.py`, where the name of the model for complex tasks is specified in `"model_name"`, and the model for simpler tasks is set in `"simple_model_name"`. 

If a fine-tuned model has been trained to improve accuracy for a specific task, the name of this model should be specified in the `"fine_tuning_models"` section for the corresponding step. For example, to use a fine-tuned model for the property inclusion step, add the fine-tuned model's name under `"ft_text_property_type"`.

Additionally, in `agent.py`, the function `def from_llm` allows setting the default model for each agent. If a fine-tuned model is available, it will be prioritized; otherwise, the default LLM model will be used. The basic LLM settings can also be adjusted for each step. Using `simple_llm` will refer to the `"simple_model_name"` in `config.py`, while using `llm` will reference `"model_name"`.

---

### 1-2) Reconstruction Settings
To reduce token usage and better extract information spread across multiple paragraphs, L2M3 performs a reconstruction process. This setting can also be adjusted. In `config.py`, the `"input_max_token"` parameter defines the token limit for input sentences, which can be modified as needed.

---

## 2) When Extracting Using the Same Process as L2M3

### 2-1) Prompt Modification
Prompts must be updated for each step to align with the target domain. For example, to categorize data specific to a desired field, modify the `PROMPT_CATEGORIZE` section in `categorize.prompt.py`. If a fine-tuned model is being used, update the `FT_CATEGORIZE` section.

Within the prompts, variables enclosed in `{}` can be observed for steps such as property inclusion/extraction or synthesis condition inclusion/extraction. Variables can be added or removed based on requirements. The possible entries for each variable can be adjusted in the `'format'` directory.

---

## 3) When Extracting Using a Process Different From L2M3

### 3-1) Renaming Extracted Information
If the target information is not `property` or `synthesis condition`, the information name needs to be changed. For instance, if the target information is renamed to `performance` instead of `property`, the following changes are required:
- Rename the directory from `text` to `performance`.
- Update all functions related to `property` in `categorize.base.py`, `agent.py`, `reader.py`, and `schema.py`. For example, rename `def get_properties` to `def get_performance`.

---

### 3-2) Skipping Specific Steps
If certain information does not need to be extracted, the relevant sections can be removed from `agent.py`. For instance, to exclude the extraction of `property`, delete all instances of `for element in jr.get_properties()`.

If an entire process needs to be modified instead of skipping specific information, changes can be made in the `base.py` code for each information type. For example, if most `property` data appears within a single paragraph and the inclusion step is unnecessary, the inclusion section in `text.base.py` can be removed, and mining can proceed directly.

---

### 3-3) Prompt Modification
Similar to the process described in **Section 2-1**, the prompts must be updated to match the characteristics of the information being extracted. For instance, if the type of extracted information changes, the prompts in `categorize.prompt.py` and `FT_CATEGORIZE` should be modified to include instructions that are specific to the new information type.

---
