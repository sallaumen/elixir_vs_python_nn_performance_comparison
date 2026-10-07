# Current-stack validation — 7 October 2026

These are single runs to confirm that the maintained programs train and evaluate on the separate test splits. They are **not comparative benchmarks**: the models, optimizers, batch sizes, and timing boundaries differ. The older screenshots belong to the [historical environment](https://github.com/sallaumen/elixir_neural_network_labs/blob/main/docs/historical-environment.md).

## Environment

- Machine: Apple M4 Max, 36 GiB memory, macOS 26.5.2; CPU execution for both implementations.
- Elixir: Erlang/OTP 29.1.1, Elixir 1.20.4, Nx 1.0.0, EXLA 1.0.0, Axon 0.9.0, Scidata 0.1.11; EXLA `host` client. Source: [Elixir commit `4fb2b69`](https://github.com/sallaumen/elixir_neural_network_labs/commit/4fb2b697c8b48ebc6a7d02096328ba5cc28db4e2).
- Python: Python 3.13.16, TensorFlow 2.21.0, Keras 3.15.1, NumPy 2.5.3; TensorFlow reported one CPU device. Source: [Python commit `cdcf008`](https://github.com/sallaumen/python_neural_network_labs/commit/cdcf0085ffee1e300e5d10507f68483ce58fc97a). The Python dependencies were pinned after this validation to the tested TensorFlow and NumPy versions.
- Each run used the maintained three-epoch configuration. Python sets the Keras random seed to 10; the Elixir runs did not set an equivalent seed.

## Completed runs

| Implementation | Dataset | Held-out test accuracy | Execution |
| --- | --- | ---: | --- |
| Elixir | MNIST | 96.69% | `Dataset.Training.MNIST.run_training_and_test/0` |
| Elixir | CIFAR-10 | 59.37% | Scidata train/test splits, `Dataset.Training.Loader`, the maintained Axon model and trainer, and `Dataset.Training.Cifar10.test_model/4` |
| Python | MNIST | 97.36% | `python -m lib.main mnist` |
| Python | CIFAR-10 | 47.44% | `python -m lib.main cifar10` |

The Elixir CIFAR-10 run used a temporary local HTTP server to supply the archive to `Scidata.CIFAR10` through its `:base_url` option. This avoided a slow transfer from the primary host; the model, preprocessing, training settings, and test evaluation matched the maintained implementation. The archive was not added to Git.

Both CIFAR-10 archives were checked against the MD5 values on the [dataset author's download page](https://www.cs.toronto.edu/~kriz/cifar.html) before use: `c32a1d4ab5d03f1284b67883e8d87530` for the binary archive used by Elixir and `c58f30108f718f92721af3b95e74349a` for the Python archive used by Keras. The files were fetched from a mirror; checksum validation confirmed they matched the published archives.

These accuracies are functional evidence from one training run each. For a defensible performance claim, follow the [comparison protocol](methodology.md), including matched experiment definitions, repeated runs, and raw timing records.
