| Model | Test MAE ↓ | Test MAE 95% CI | Δ test MAE vs MobileNetV2 [95% CI], Wilcoxon p | Test RMSE ↓ | Test R² ↑ | Val MAE ↓ | Val RMSE ↓ | Val R² ↑ | LODO MAE d2-9 ↓ | Params (M) ↓ | Size fp32 (MB) ↓ | MACs (M) ↓ | CPU ms ↓ | GPU ms ↓ |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| MobileNetV2 (Keras, published) | 0.706 | [0.580, 0.842] | -0.036 [-0.172, +0.102], p = 0.41 | 0.873 | 0.901 | 0.658 | 0.888 | 0.903 | 0.884 | 2.34 | 9.36 | n/a | n/a* | n/a |
| MobileNetV2 | 0.742 ± 0.099 | [0.635, 0.858] | reference | 0.926 ± 0.135 | 0.887 ± 0.035 | 0.738 ± 0.161 | 0.915 ± 0.206 | 0.893 ± 0.052 | 1.006 ± 0.110 | 2.31 | 9.22 | 97.9 | 11.78 | 5.33 |
| ResNet-18 | 0.840 ± 0.047 | [0.701, 0.992] | +0.097 [-0.053, +0.253], p = 0.37 | 1.074 ± 0.032 | 0.850 ± 0.009 | 0.861 ± 0.103 | 1.093 ± 0.117 | 0.852 ± 0.031 | 1.049 ± 0.053 | 11.21 | 44.84 | 592.2 | 23.52 | **2.80** |
| EfficientNet-B0 | 0.703 ± 0.054 | [0.590, 0.825] | -0.039 [-0.184, +0.105], p = 0.63 | 0.880 ± 0.070 | 0.899 ± 0.016 | **0.640 ± 0.053** | **0.809 ± 0.074** | **0.919 ± 0.014** | 0.967 ± 0.022 | 4.09 | 16.36 | 126.1 | 17.77 | 8.69 |
| ShuffleNetV2 | **0.667 ± 0.040** | [0.555, 0.782] | -0.075 [-0.202, +0.052], p = 0.19 | **0.815 ± 0.040** | **0.913 ± 0.008** | 0.795 ± 0.019 | 1.070 ± 0.022 | 0.860 ± 0.006 | **0.864 ± 0.028** | **1.32** | **5.28** | **47.0** | **10.87** | 6.38 |

Notes: ± is the standard deviation over 5 seeds (the published row is one trained model, so no ±). **Bold marks the best mean among the four models in a column; it does not mean the difference is statistically significant** (see the Δ column: paired difference to MobileNetV2 over the same 58 test images, bootstrap 95% CI, Wilcoxon signed-rank p). The published row is a reference and is not ranked.
Test = 58 images, Val = 57 images (Table 5 of the paper used Val). The CI is a bootstrap over the 58 test images (10,000 resamples) for the seed-averaged MAE.
LODO = leave-one-day-out MAE averaged over days 2-9 (the published row: one seed). Size = parameters x 4 bytes. MACs = conv + linear layers at 128x128.
CPU = median single-thread latency, batch 1, PyTorch fp32. GPU = median batch-1 latency on the Colab GPU.
* Published model, TFLite fp16: 4.63 MB file, 4.40 ms median CPU latency (1 thread). Different runtime, so not comparable with the PyTorch column and not shown in it.