# rebecca

A tool for checking if a given netlist is side-channel analysis resistant.

## Prerequisites

### Python Dependencies

Rebecca has a couple of Python dependencies. You can run
```console
$ pip install --user -r python_requirements.txt
```
to install those dependencies.

### Yosys

Rebecca requires the [Yosys Open SYnthesis Suite](https://github.com/YosysHQ/yosys) to
be installed.

## Usage

```console
$ ./verify -h
usage: ./verify [-h] [-v] [-p <netlist> <top module>] [-o]
                [-c <netlist> <order> <labeling> <mode>]
                [-i <netlist> <order> <labeling>]

A tool for checking if a given netlist is side-channel analysis resistant

optional arguments:
  -h, --help            show this help message and exit
  -v, --version         show program's version number and exit
  -p <netlist> <top module>, --parse-verilog <netlist> <top module>
                        parse verilog file and generate labeling template
  -o, --optimized       run verification in parallel
  -c <netlist> <order> <labeling> <mode>, --check <netlist> <order> <labeling> <mode>
                        check if a netlist <netlist> is <order>-order secure
                        with the <labeling> as initial labeling; mode = s
                        (stable) | t (transient)
  -i <netlist> <order> <labeling>, --independence-check <netlist> <order> <labeling>
                        check if a netlist <netlist> is <order>-order
                        independent with the <labeling> as initial labeling
```

## Example

The following steps illustrate how to use rebecca for verifying the DOM AND gate.

1. In a first step, the netlist needs to be parsed using Yosys:
```console
$ ./verify.py --parse-verilog benchmarks/first_order/dom_and/dom_and.v dom_and
```

2. As a result, the verilog netlist is parsed into a .json file and a the labeling
template is produced. Open the file `benchmarks/first_order/dom_and/dom_and.txt` and
label the input nodes as follows:
```
ClkxCI_2: unimportant
QxDO_10: unimportant
QxDO_9: unimportant
RstxBI_3: unimportant
XxDI_4: share 1
XxDI_5: share 1
YxDI_6: share 2
YxDI_7: share 2
ZxDI_8: mask
```

3. Now, the following command can be used to perform the check of the netlist:
```console
$ ./verify.py --check benchmarks/first_order/dom_and/dom_and.json 1 benchmarks/first_order/dom_and/dom_and.txt s
```
4. As the DOM AND gate is first-order secure, this should produce the following output:
```console
(True, [])
```

## References

- [Formal Verification of Masked Hardware Implementations in the Presence of Glitches](https://eprint.iacr.org/2017/897.pdf)
