## **SciCode Evaluation using `inspect_ai`**

### 1. Set Up Your API Keys

Users can follow [`inspect_ai`'s official documentation](https://inspect.ai-safety-institute.org.uk/#getting-started) to setup correpsonding API keys depending on the types of models they would like to evaluate.

### 2. Setup Command Line Arguments if Needed

In most cases, after users setting up the key, they can directly start the SciCode evaluation via the following command.

```bash
inspect eval scicode.py --model <your_model> --temperature 0
```

However, there are some additional command line arguments that could be useful as well.

- `--max-connections`: Maximum amount of API connections to the evaluated model.
- `--limit`: Limit of the number of samples to evaluate in the SciCode dataset.
- `-T split=validation/test`: Whether the user wants to run on the small `validation` set (15 samples) or the large `test` set (65 samples).
- `-T output_dir=<your_output_dir>`: This changes the default output directory (`./tmp`).
- `-T h5py_file=<your_h5py_file>`: This is used if your h5py file is not downloaded in the recommended directory.
- `-T with_background=True/False`: Whether to include problem background.
- `-T mode=normal/gold/dummy`: This provides two additional modes for sanity checks.
    - `normal` mode is the standard mode to evaluate a model
    - `gold` mode can only be used on the validation set which loads the gold answer
    - `dummy` mode does not call any real LLMs and generates some dummy outputs

For example, user can run five samples on the validation set with background as

```bash
inspect eval scicode.py \
    --model openai/gpt-4o \
    --temperature 0 \
    --limit 5 \
    -T split=validation \
    -T output_dir=./tmp/val \
    -T with_background=True \
    -T mode=normal
```

User can run the evaluation on `Deepseek-v3` using together ai via the following command:

```bash
export TOGETHER_API_KEY=<YOUR_API_KEY>
inspect eval scicode.py \
    --model together/deepseek-ai/DeepSeek-V3 \
    --temperature 0 \
    --max-connections 2 \
    --max-tokens 32784 \
    -T output_dir=./tmp/deepseek-v3 \
    -T with_background=False
```

For more information regarding `inspect_ai`, we refer users to its [official documentation](https://inspect.ai-safety-institute.org.uk/).

### Extra: How SciCode are Evaluated Under the Hood?

During the evaluation, the sub-steps of each main problem of SciCode are passed in order to the evalauted LLM with necessary prompts and LLM responses for previous sub-steps. The generated Python code from LLM will be parsed and saved to disk, which will be used to run on test cases to determine the pass or fail for the sub-steps. The main problem will be considered as solved if the LLM can pass all sub-steps of the main problem. 

### Extra: Reproducibility of `inspect_ai` Integration 
We use the SciCode `inspect_ai` integration to evaluate OpenAI's GPT-4o, and we compare it with the original way of evaluation. Below shows the comparison of two ways of the evaluations. 

*[💡It should be noted that it is common to have slightly different results due to the randomness of LLM generations.]*

| Methods                   | Main Problem Resolve Rate           | <span style="color:grey">Subproblem</span>            |
|---------------------------|-------------------------------------|-------------------------------------------------------|
| `inspect_ai` Evaluation   | <div align="center">**3.1 (2/65)**</div>   | <div align="center" style="color:grey">25.1</div>     |
| Original Evaluation       | <div align="center">**1.5 (1/65)**</div>   | <div align="center" style="color:grey">25.0</div>     |
