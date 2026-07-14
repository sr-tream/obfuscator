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

## Function annotations

Use Clang's `annotate` attribute to select individual functions. The
annotation is evaluated by the normal optimization pipeline, so compile the
translation unit with `-O1` or higher.

```c
#define OBFUSCATE(...) __attribute__((annotate(#__VA_ARGS__)))

OBFUSCATE(fla split split_num=3 sub sub_loop=3)
int protected_function(int value) {
  if (value > 0)
    return value + 1;
  return value - 1;
}
```

Supported function annotations are `fla`, `bcf`, `split`, and `sub`. Use
`bcf_prob=<1-100>`, `bcf_loop=<n>`, `split_num=<2-10>`, and `sub_loop=<n>` to
configure their individual functions. `sobf` remains a module-wide pass and
is enabled with `-mllvm -sobf`.

The flattening pass needs a function with more than one basic block at the
time it runs; simple branches may be simplified before it can transform them.



Also, to increase build speed you can rebuild only `opt` utility (because obfuscator add passes to optimization pipeline). And replace `opt` in you toolchain.
