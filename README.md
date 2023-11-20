# UltraFastBERT
The repository for the code of the UltraFastBERT paper, based directly off the crammedBERT project.

## Organisation

1. The `training` folder contains a clone of the crammedBERT repository from the beginning of October 2023. A few new configurations and small modifications have been made to enable the use of FFFs. A masking implementation (i.e. an implementation of FFFs that offers no speed advantage over FFs but simulates its selective engagement of neurons by masking) is provided for training and downstream finetuning.
2. The `benchmark_cpu` folder contains C++ code using Intel MKL 2023.2.0 to impelement accelerated CPU versions of FFF inference as well as baseline DMM implementations of the traditional FF layers.
3. `bechmark_pytorch` folder contains the C++ code for the "Native fused" and "PyTorch BMM" implementations of both FF and FFF inference.
4. `benchar_cuda` folder contains the C++/CUDA kernel code for the "Naive CUDA" implementations of FF and FFF.

## Reproducing the results from weights

The configuration and weights for UltraFastBERT-1x11-long can be found on HuggingFace:

[https://huggingface.co/pbelcak/UltraFastBERT-1x11-long](https://huggingface.co/pbelcak/UltraFastBERT-1x11-long)

These files been produced and uploaded using `training/load_local_model.py` with `impl.push_to_huggingface_hub=True`.

UltraFastBERT-1x11-long, as a model, is an instance of our small extension of the crammedBERT setup.
You can simply enter the `training` directory and follow the steps given in the crammingBERT README to use HuggingFace `AutoTokenizer` and `AutoModelForMaskedLM`, with the difference that you want UltraFastBERT-1x11-long, and not crammedBERT.

```
import cramming
from transformers import AutoModelForMaskedLM, AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained("pbelcak/UltraFastBERT-1x11-long")
model = AutoModelForMaskedLM.from_pretrained("pbelcak/UltraFastBERT-1x11-long")

text = "Replace me by any text you'd like."
encoded_input = tokenizer(text, return_tensors='pt')
output = model(**encoded_input)
```

## Reproducing the results from scratch

1. To reproduce our training and finetuning results, simply head straight down to the `training` folder and follow the instructions of the README there.
2. To reproduce our CPU speed benchmarking results, head to `benchmark_cpu`. If you're on Windows, the easiest way to compile&run the code might be to use Visual Studio 2022 Community with the Intel oneAPI extension. The other option is to use the Intel compilers directly (more information on the Intel oneAPI "Getting started" websites).
3. `benchmark_pytorch` results can be reproduced by running `python main.py` in the folder. The outcomes of these runs are automatically put into a SQLite `results.db` file for the ease of inspection.
4. `benchmark_cuda` requires the CUDA Toolkit. Once installed, using `python setup.py install` in the extension folder will do the CUDA code compilation for you and prepare a module that can be imported.
