---
title: Paddlepaddle-gpu Issue
tags: [issue, fixed]
description: Paddlepaddle-gpu Issue
---

# Installation

## Issue

When you try to use `pip install paddlepaddle-gpu`, you may get the following error:

```bash
Traceback (most recent call last):
  File "test_uie.py", line 46, in <module>
    results = ie(texts)
  File "/nfs/users/zhoutong/miniconda3/envs/pretraining/lib/python3.7/site-packages/paddlenlp/taskflow/taskflow.py", line 524, in __call__
    results = self.task_instance(inputs)
  File "/nfs/users/zhoutong/miniconda3/envs/pretraining/lib/python3.7/site-packages/paddlenlp/taskflow/task.py", line 361, in __call__
    outputs = self._run_model(inputs)
  File "/nfs/users/zhoutong/miniconda3/envs/pretraining/lib/python3.7/site-packages/paddlenlp/taskflow/information_extraction.py", line 613, in _run_model
    results = self._multi_stage_predict(raw_inputs)
  File "/nfs/users/zhoutong/miniconda3/envs/pretraining/lib/python3.7/site-packages/paddlenlp/taskflow/information_extraction.py", line 678, in _multi_stage_predict
    result_list = self._single_stage_predict(examples)
  File "/nfs/users/zhoutong/miniconda3/envs/pretraining/lib/python3.7/site-packages/paddlenlp/taskflow/information_extraction.py", line 508, in _single_stage_predict
    self.input_handles[0].copy_from_cpu(input_ids.numpy())
  File "/nfs/users/zhoutong/miniconda3/envs/pretraining/lib/python3.7/site-packages/paddle/fluid/inference/wrapper.py", line 36, in tensor_copy_from_cpu
    self.copy_from_cpu_bind(data)
RuntimeError: (PreconditionNotMet) Cannot load cudnn shared library. Cannot invoke method cudnnGetVersion.
  [Hint: cudnn_dso_handle should not be null.] (at /paddle/paddle/phi/backends/dynload/cudnn.cc:59)
```

## Solution
Install `CUDA 11.1` and `paddlepaddle-gpu`
```bash
conda install cudatoolkit==11.1
python -m pip install paddlepaddle-gpu==2.3.2.post111 -f https://www.paddlepaddle.org.cn/whl/linux/mkl/avx/stable.html
```
Test your installation
```bash
export LD_LIBRARY_PATH=/path/to/cudnn/lib
python -c "import paddle;paddle.utils.run_check()"
```
