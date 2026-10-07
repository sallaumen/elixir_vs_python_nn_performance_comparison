# Comparison methodology

## What the repositories currently measure

The original project and its maintained implementations answer a useful engineering question: how do two ecosystems express and train image classifiers? They do not isolate the performance cost of Elixir versus Python. Both rely on native numerical backends, and the experiment definitions differ.

| Dimension | Elixir implementation | Python implementation |
| --- | --- | --- |
| MNIST model | One 128-unit dense layer, dropout 0.5, 10-class output | Two 128-unit dense layers, dropout 0.5, 10-class output |
| CIFAR-10 model | Two convolutions with batch normalization and pooling, dropout 0.5, 10-class output | Two convolutions with pooling, no batch normalization or dropout, 10-class output |
| CIFAR-10 optimizer | Adam | SGD |
| Default training epochs | 3 | 3 |
| Batch size | 16 in the maintained loader | Keras default 32 in the current CLI |
| Evaluation | `run_training_and_test/0` downloads the separate test split | Current CLI evaluates the separate test split |
| Backend | EXLA client defaults to host in project config | TensorFlow device depends on the installed environment |

These details are based on the maintained source in the linked implementation repositories. The Elixir repository [archives its original toolchain and lockfile](https://github.com/sallaumen/elixir_neural_network_labs/blob/main/docs/historical-environment.md). Historical screenshots should remain labeled as historical unless the experiment is rerun.

## Protocol for a future performance claim

1. Specify the dataset release, train/test split, preprocessing, batch size, model layers, initialization, optimizer, loss, learning rate, and epoch count in one shared experiment specification.
2. Implement that specification in both repositories and add checks for input/output shapes and parameter counts. Preserve the historical artifacts and configuration separately.
3. Run both on the same machine and device class. Record CPU/GPU model, memory, operating system, runtime and library versions, EXLA/TensorFlow backend settings, and the exact Git commits.
4. Define the timing boundary. Report dataset download, preprocessing, compilation or warm-up, training, and inference separately. Synchronize accelerator work before stopping a timer where required.
5. Run multiple trials with documented seeds and report the distribution, not a single best run. Report held-out loss and accuracy with the runtime results.
6. Publish commands and raw measurements so another person can reproduce the summary.

Until that protocol is implemented, describe the project as a comparison of approaches and historical experiments, not a head-to-head speed result.
