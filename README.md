# reversevm  • [![tests](https://github.com/abigger87/reversevm/actions/workflows/tests.yml/badge.svg)](https://github.com/abigger87/reversevm/actions/workflows/tests.yml) [![lints](https://github.com/abigger87/reversevm/actions/workflows/lints.yml/badge.svg)](https://github.com/abigger87/reversevm/actions/workflows/lints.yml) ![GitHub](https://img.shields.io/github/license/abigger87/reversevm) ![GitHub package.json version](https://img.shields.io/github/package-json/v/abigger87/reversevm)

Notes on Reverse Engineering EVM Bytecode.

## Blueprint

```ml
lib
├─ ds-test
├─ solmate
src
├─ tests
│  └─ Greeter.t — "Greeter Tests"
└─ Greeter — "A Minimal Greeter Contract"
```

## Development

### First time with Forge/Foundry?

Don't have [rust](https://www.rust-lang.org/tools/install) installed?
Run
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Then, install [foundry](https://github.com/gakonst/foundry) with:
```bash
cargo install --git https://github.com/gakonst/foundry --bin forge --locked
```

### Dependencies

```bash
yarn setup
```

### Run Tests

```bash
yarn test
```

## Reverse Engineering

### Prerequisites

Requirement: Install `r2`:
```bash
git clone https://github.com/radareorg/radare2
cd radare2 ; sys/install.sh
```

(UbInstall libjansson-dev:
```bash
sudo apt install libjansson-dev
```

Install `evm` using r2pm:
```bash
r2pm -gi evm
```

Install the [iaito](https://github.com/radareorg/iaito) graphical interface:
```bash
r2pm -ci iaito
```

### Disassembling

Compile `Greeter.sol` to binary code using `solc`:
```bash
solc ./src/Greeter.sol --bin-runtime -o ./out/ --overwrite
ls ./out/
```

We need to use `--bin-runtime` instead of `--bin` to exclude the prepended code for blockchain placement.

Now convert the hexadecimal format from `solc` into pure binary format using `rax2`:
```bash
rax2 -s < ./out/Greeter.bin-runtime > ./out/Greeter.bin-runtime.bin
```

Analyze with `r2`:
```bash
r2 -a evm ./out/Greeter.bin-runtime.bin
```

Then, enter `aa` for the first prompt:
```bash
[0x00000000]> aa
[Warning: set your favourite calling convention in `e anal.cc=?`
[x] Analyze all flags starting with sym. and entry0 (aa)
```

Next, disassemble 30 instructions with `pd 30`:
```bash
[0x00000000]> pd 30
┌ 1077: fcn.00000000 ();
│           0x00000000      60
│           0x00000001      80
...
```



## License

[AGPL-3.0-only](https://github.com/abigger87/reversevm/blob/master/LICENSE)

# Resources & Acknowledgements

- [Radare2 Book](https://book.rada.re/) (select `ayu` color trust me :)
- [Practical Eth Decompilation](https://blog.ret2.io/2018/05/16/practical-eth-decompilation/)
- [ICO Security Tips radare2](https://blog.positive.com/reversing-evm-bytecode-with-radare2-ab77247e5e53)
- [foundry](https://github.com/gakonst/foundry)
- [solmate](https://github.com/Rari-Capital/solmate)
- [Georgios Konstantopoulos](https://github.com/gakonst) for [forge-template](https://github.com/gakonst/forge-template) resource.

## Disclaimer

_These smart contracts are being provided as is. No guarantee, representation or warranty is being made, express or implied, as to the safety or correctness of the user interface or the smart contracts. They have not been audited and as such there can be no assurance they will work as intended, and users may experience delays, failures, errors, omissions, loss of transmitted information or loss of funds. We are not liable for any of the foregoing. Users should proceed with caution and use at their own risk._
