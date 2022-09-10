---
layout: hub_detail
background-class: hub-background
body-class: hub
title: ResNet50
summary: ResNet50 model trained with mixed precision using Tensor Cores.
category: researchers
image: nvidia_logo.png
author: NVIDIA
tags: [vision]
github-link: https://github.com/NVIDIA/DeepLearningExamples/tree/master/PyTorch/Classification/ConvNets/resnet50v1.5
github-id: NVIDIA/DeepLearningExamples
featured_image_1: classification.jpg
featured_image_2: no-image
accelerator: cuda
order: 10
demo-model-link: https://huggingface.co/spaces/pytorch/ResNet50
---


### �� ����

***ResNet50 v1.5***���� [original ResNet50 v1 model](https://arxiv.org/abs/1512.03385)�� ������ �����Դϴ�.

v1�� v1.5�� �������� �ٿ���ø��� �ʿ��� ���� ��Ͽ��� v1�� ù ��° 1x1 ������ǿ��� ��Ʈ���̵� = 2�� ���� �ݸ� v1.5�� 3x3 ������ǿ��� ��Ʈ���̵� = 2�� ���´ٴ� ���Դϴ�.

�̷��� ���̴� ResNet50 v1.5�� v1���� ���� �� ��Ȯ�ϰ� ��������(\~0.5% top1) �ణ�� �������� ����(\~5% imgs/sec)�� �ֽ��ϴ�.

The model is initialized as described in [Delving deep into rectifiers: Surpassing human-level performance on ImageNet classification](https://arxiv.org/pdf/1502.01852.pdf)

���� [Delving deep into rectifiers: Surpassing human-level performance on ImageNet classification](https://arxiv.org/pdf/1502.01852.pdf)�� ����� ��� �ʱ�ȭ�˴ϴ�.

This model is trained with mixed precision using Tensor Cores on Volta, Turing, and the NVIDIA Ampere GPU architectures. Therefore, researchers can get results over 2x faster than training without Tensor Cores, while experiencing the benefits of mixed precision training. This model is tested against each NGC monthly container release to ensure consistent accuracy and performance over time.

�� ���� Volta, Turing �� NVIDIA Ampere GPU ��Ű��ó�� Tensor �ھ ����Ͽ� ȥ�� ���е��� �н��˴ϴ�. ���� �����ڵ��� ȥ�� ���� ������ ������ �����ϸ鼭 Tensor Core ���� �н��ϴ� �ͺ��� 2�� �̻� ���� ����� ���� �� �ֽ��ϴ�. �� ���� �ð��� ������ ���� �ϰ��� ��Ȯ���� ������ �����ϱ� ���� �� NGC ���� �����̳� �������� ���� �׽�Ʈ�˴ϴ�.

Note that the ResNet50 v1.5 model can be deployed for inference on the [NVIDIA Triton Inference Server](https://github.com/NVIDIA/trtis-inference-server) using TorchScript, ONNX Runtime or TensorRT as an execution backend. For details check [NGC](https://ngc.nvidia.com/catalog/resources/nvidia:resnet_for_triton_from_pytorch)

ResNet50 v1.5 ���� TorchScript, ONNX Runtime �Ǵ� TensorRT�� ���� �鿣��� ����Ͽ� [NVIDIA Triton Inference Server](https://github.com/NVIDIA/trtis-inference-server)���� �߷��� ���� ��ġ�� �� �ֽ��ϴ�. �ڼ��� ������ [NGC](https://ngc.nvidia.com/catalog/resources/nvidia:resnet_for_triton_from_pytorch)�� Ȯ���Ͻʽÿ�.

### ���� ���

�Ʒ� ���������� ���� �Ʒõ� ***ResNet50 v1.5*** ���� ����Ͽ� ***�̹���***�� ���� �߷��� ���� �ϰ� ����� ������ ���Դϴ�.

������ �����Ϸ��� �� ���� �߰� ���̽� ��Ű���� ��ġ�Ǿ� �־�� �մϴ�. �̴� �̹����� ��ó���ϰ� �ð�ȭ�ϴ� �� �ʿ��մϴ�.
```python
!pip install validators matplotlib
```

```python
import torch
from PIL import Image
import torchvision.transforms as transforms
import numpy as np
import json
import requests
import matplotlib.pyplot as plt
import warnings
warnings.filterwarnings('ignore')
%matplotlib inline

device = torch.device("cuda") if torch.cuda.is_available() else torch.device("cpu")
print(f'Using {device} for inference')
```

IMAGENET �����ͼ¿��� ���� �Ʒõ� ���� �ε��մϴ�.
```python
resnet50 = torch.hub.load('NVIDIA/DeepLearningExamples:torchhub', 'nvidia_resnet50', pretrained=True)
utils = torch.hub.load('NVIDIA/DeepLearningExamples:torchhub', 'nvidia_convnets_processing_utils')

resnet50.eval().to(device)
```

���� �Է� �����͸� �غ��մϴ�.
```python
uris = [
    'http://images.cocodataset.org/test-stuff2017/000000024309.jpg',
    'http://images.cocodataset.org/test-stuff2017/000000028117.jpg',
    'http://images.cocodataset.org/test-stuff2017/000000006149.jpg',
    'http://images.cocodataset.org/test-stuff2017/000000004954.jpg',
]

batch = torch.cat(
    [utils.prepare_input_from_uri(uri) for uri in uris]
).to(device)
```

�߷��� �����մϴ�. `pick_n_best(predictions=output, n=topN)` helper �Լ��� ����Ͽ� �𵨿� ���� ���� ���ɼ��� ���� ������ N�� �����մϴ�.
```python
with torch.no_grad():
    output = torch.nn.functional.softmax(resnet50(batch), dim=1)
    
results = utils.pick_n_best(predictions=output, n=5)
```

����� ǥ���մϴ�.
```python
for uri, result in zip(uris, results):
    img = Image.open(requests.get(uri, stream=True).raw)
    img.thumbnail((256,256), Image.ANTIALIAS)
    plt.imshow(img)
    plt.show()
    print(result)

```

### ���λ���
For detailed information on model input and output, training recipies, inference and performance visit:
�� �Է� �� ���, �н� ���, �߷� �� ���� � ���� �� �ڼ��� ������ [github](https://github.com/NVIDIA/DeepLearningExamples/tree/master/PyTorch/Classification/ConvNets/resnet50v1.5) �� and/or [NGC](https://ngc.nvidia.com/catalog/resources/nvidia:resnet_50_v1_5_for_pytorch)���� �� �� �ֽ��ϴ�.


### ������

 - [Original ResNet50 v1 paper](https://arxiv.org/abs/1512.03385)
 - [Delving deep into rectifiers: Surpassing human-level performance on ImageNet classification](https://arxiv.org/pdf/1502.01852.pdf)
 - [model on github](https://github.com/NVIDIA/DeepLearningExamples/tree/master/PyTorch/Classification/ConvNets/resnet50v1.5)
 - [model on NGC](https://ngc.nvidia.com/catalog/resources/nvidia:resnet_50_v1_5_for_pytorch)
 - [pretrained model on NGC](https://ngc.nvidia.com/catalog/models/nvidia:resnet50_pyt_amp)
 