## Configure LLVM 22.x with obfuscation support

The `main` branch targets LLVM 22.x. Use the `release/18.x` branch for LLVM 18.x.

```bash
git clone -b main https://github.com/sr-tream/obfuscator
cd obfuscator
git submodule update --init llvm-project
cd llvm-project
git apply ../obfuscator.patch
```

Now you can build `llvm-project` to get llvm with obfuscation.



Also, to increase build speed you can rebuild only `opt` utility (because obfuscator add passes to optimization pipeline). And replace `opt` in you toolchain.
