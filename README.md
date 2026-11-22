# Banana ripening-day regression: backbone comparison

Code and results for the backbone comparison added in the paper revision: MobileNetV2 against ResNet-18, EfficientNet-B0 and ShuffleNetV2 on a regression task that predicts the ripening day (1-10) of a banana from one 128x128 photograph.

## What is here

| Path | Content |
| --- | --- |
| `notebooks/banana_model_comparison.ipynb` | The full experiment as executed on Google Colab (T4 GPU), with outputs. |
| `results/` | CSVs written by the notebook and the final comparison table. |
| `results/figures/` | The two comparison figures used in the paper. |

## Protocol

- Data: 571 photographs of one bunch, split 456 / 57 / 58 (train / validation / test), stratified by day, `random_state=42`, 80/10/10. The notebook stops if the split or the published Keras model's metrics do not match the paper.
- All four models use the same setup: frozen ImageNet backbone (torchvision `IMAGENET1K_V1` weights, ImageNet mean and standard deviation), 128x128 input, head = pooled features -> Dense(64, ReLU) -> Dropout(0.3) -> Linear(1), inverse-frequency weighted MSE, Adam (learning rate 1e-3), batch size 16, 20 epochs, final weights (no model selection), seeds 0-4.
- Because the backbones are frozen, each backbone's features are computed once and only the head is trained.
- Uncertainty: bootstrap 95% interval of the seed-averaged test MAE (10,000 resamples of the 58 test images); paired per-image difference against the PyTorch MobileNetV2 with a bootstrap interval and a Wilcoxon signed-rank test; leave-one-day-out MAE.
- Efficiency: parameters, fp32 size, multiply-accumulate operations at 128x128, single-thread CPU latency (batch 1, PyTorch) and GPU batch-1 latency.

## Results (test set, 58 images)

Mean ± SD over five seeds. The first row is the published Keras model (one trained model).

| Model | Test MAE (days) | 95% CI | Test RMSE | Test R² | Params (M) | MACs (M) |
| --- | --- | --- | --- | --- | --- | --- |
| MobileNetV2 (Keras, published) | 0.706 | [0.580, 0.842] | 0.873 | 0.901 | 2.34 | n/a |
| MobileNetV2 | 0.742 ± 0.099 | [0.635, 0.858] | 0.926 ± 0.135 | 0.887 ± 0.035 | 2.31 | 97.9 |
| ResNet-18 | 0.840 ± 0.047 | [0.701, 0.992] | 1.074 ± 0.032 | 0.850 ± 0.009 | 11.21 | 592.2 |
| EfficientNet-B0 | 0.703 ± 0.054 | [0.590, 0.825] | 0.880 ± 0.070 | 0.899 ± 0.016 | 4.09 | 126.1 |
| ShuffleNetV2 | 0.667 ± 0.040 | [0.555, 0.782] | 0.815 ± 0.040 | 0.913 ± 0.008 | 1.32 | 47.0 |

No paired difference from MobileNetV2 is statistically significant (Wilcoxon p between 0.19 and 0.63). The models differ mainly in cost. The full table, with validation metrics, leave-one-day-out results and latency, is in `results/final_comparison_table.md`.

## Running the notebook

1. Open the notebook in Colab and select a T4 GPU runtime.
2. The notebook reads its inputs from a Google Drive folder `banana_project/`: `manifest.csv`, one folder of photographs per day (`Day1` ... `Day10`), the published Keras model, and `eval/predictions.csv` from the original evaluation. **These files are not included in this repository.** Place them in that layout, or edit the paths in the first two code cells.
3. Run all cells. The run is resumable: a model already present in the result CSVs is skipped. It takes roughly 10-20 minutes.

`requirements.txt` lists the packages used; versions are not pinned because the notebook ran in Colab's default environment.

## Limitations

All images come from a single bunch and the test set has 58 images, so the intervals are wide. Only frozen backbones were compared. The published Keras model and its PyTorch re-implementation use different framework weights and preprocessing, so their numbers differ by less than the seed spread. CPU latency is measured in PyTorch and is not comparable with a TFLite latency.

## Licence

MIT, see `LICENSE`.
