# For Developers of the TEN Framework

The TEN framework includes three types of test suites:

* Unit Tests
* Smoke Tests
* Integration Tests

The TEN framework uses `gtest` as the testing framework for unit tests and smoke tests, and `pytest` as the testing framework for integration tests.

## Unit Tests

The source directory for unit tests is `tests/ten_runtime/unit`. If you need to add new unit test cases, please place them in the `tests/ten_runtime/unit directory`.

For C++ projects, after successful compilation, you can navigate to the output directory (e.g., `out/linux/x64/tests/standalone/`, depending on the target OS and CPU) and execute the following command:

```shell
./ten_runtime_unit_test
```

## Smoke Tests

The source directory for smoke tests is `tests/ten_runtime/smoke`. If you need to add new smoke test cases, please place them in the `tests/ten_runtime/smoke` directory.

Navigate to the output directory (e.g., `out/linux/x64/tests/standalone`, depending on the target OS and CPU) and execute the following command:

```shell
./ten_runtime_smoke_test
```

## Loop Testing

You can perform multiple rounds of testing using the following commands.

To run only unit tests:

```shell
failed=0; for i in {1..100}; do if [ ${failed} != 0 ]; then echo "error occurred:" ${failed}; break; fi; ./ten_runtime_unit_test; failed=$?; done;
```

To run only smoke tests:

```shell
failed=0; for i in {1..100}; do if [ ${failed} != 0 ]; then echo "error occurred:" ${failed}; break; fi; ./ten_runtime_smoke_test; failed=$?; done;
```

To run both unit tests and smoke tests:

```shell
failed=0; for i in {1..100}; do if [ ${failed} != 0 ]; then echo "error occurred:" ${failed}; break; fi; ./ten_runtime_unit_test; failed=$?; if [ ${failed} != 0 ]; then echo "error occurred:" ${failed}; break; fi; ./ten_runtime_smoke_test; failed=$?; done;
```

## Integration Tests

Integration tests are black-box tests for the TEN framework. The source directory for integration tests is `tests/ten_runtime/integration`. These tests are used to validate real-world execution scenarios.

The file structure for integration tests is as follows:

```text
tests/ten_runtime/integration/
  ├── test_1/
  │    ├── test_case.py
  │    └── ...
  ├── test_2/
  │    ├── test_case.py
  │    └── ...
  └── ...
```

To execute the integration tests, use the following command:

```shell
cd path/to/out/
pytest tests/ten_runtime/integration/
```

## Testing Tricks

### Linux

On Linux, you can use the following technique to slow down the execution of TEN, which can help in triggering timing-related bugs.

```shell
sudo apt install util-linux
cpulimit -f -l 50 -- taskset 0x3 ...
```

* `taskset 0x1`: Use only 1 CPU core to run the program.
* `taskset 0x3`: Use only 2 CPU cores to run the program.
# 适用于 TEN 框架的开发人员

TEN 框架包括三种类型的测试套件：

*   单元测试
*   冒烟测试
*   集成测试

TEN 框架使用 `gtest` 作为单元测试和冒烟测试的测试框架，使用 `pytest` 作为集成测试的测试框架。

## 单元测试

单元测试的源目录是 `tests/ten_runtime/unit`。如果您需要添加新的单元测试用例，请将它们放在 `tests/ten_runtime/unit directory` 中。

对于 C++ 项目，在成功编译后，您可以导航到输出目录（例如，`out/linux/x64/tests/standalone/`，具体取决于目标操作系统和 CPU）并执行以下命令：

```shell
./ten_runtime_unit_test
```

## 冒烟测试

冒烟测试的源目录是 `tests/ten_runtime/smoke`。如果您需要添加新的冒烟测试用例，请将它们放在 `tests/ten_runtime/smoke` 目录中。

导航到输出目录（例如，`out/linux/x64/tests/standalone`，具体取决于目标操作系统和 CPU）并执行以下命令：

```shell
./ten_runtime_smoke_test
```

## 循环测试

您可以使用以下命令执行多轮测试。

仅运行单元测试：

```shell
failed=0; for i in {1..100}; do if [ ${failed} != 0 ]; then echo "error occurred:" ${failed}; break; fi; ./ten_runtime_unit_test; failed=$?; done;
```

仅运行冒烟测试：

```shell
failed=0; for i in {1..100}; do if [ ${failed} != 0 ]; then echo "error occurred:" ${failed}; break; fi; ./ten_runtime_smoke_test; failed=$?; done;
```

同时运行单元测试和冒烟测试：

```shell
failed=0; for i in {1..100}; do if [ ${failed} != 0 ]; then echo "error occurred:" ${failed}; break; fi; ./ten_runtime_unit_test; failed=$?; if [ ${failed} != 0 ]; then echo "error occurred:" ${failed}; break; fi; ./ten_runtime_smoke_test; failed=$?; done;
```

## 集成测试

集成测试是 TEN 框架的黑盒测试。集成测试的源目录是 `tests/ten_runtime/integration`。这些测试用于验证实际的执行场景。

集成测试的文件结构如下：

```text
tests/ten_runtime/integration/
  ├── test_1/
  │    ├── test_case.py
  │    └── ...
  ├── test_2/
  │    ├── test_case.py
  │    └── ...
  └── ...
```

要执行集成测试，请使用以下命令：

```shell
cd path/to/out/
pytest tests/ten_runtime/integration/
```

## 测试技巧

### Linux

在 Linux 上，您可以使用以下技术来减慢 TEN 的执行速度，这有助于触发与时序相关的错误。

```shell
sudo apt install util-linux
cpulimit -f -l 50 -- taskset 0x3 ...
```

*   `taskset 0x1`: 仅使用 1 个 CPU 核心来运行程序。
*   `taskset 0x3`: 仅使用 2 个 CPU 核心来运行程序。
