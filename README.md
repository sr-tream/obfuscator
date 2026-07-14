To get obfuscator select branch what version you want

Current version: [main](https://github.com/sr-tream/obfuscator/tree/main) (llvm 22.x)

Previous versions: 

- [release/18.x](https://github.com/sr-tream/obfuscator/tree/release/18.x)

- [release/17.x](https://github.com/sr-tream/obfuscator/tree/release/17.x)

- [release/16.x](https://github.com/sr-tream/obfuscator/tree/release/16.x)

- [release/15.x](https://github.com/sr-tream/obfuscator/tree/release/15.x)

- [release/14.x](https://github.com/sr-tream/obfuscator/tree/release/14.x)

# Usage

## How to apply obfuscation

### Via function attributes

```c++
__attribute( ( __annotate__( ( "obfuscator options separated by space" ) ) ) )
```

### Via compiler options

```bash
clang -mllvm <obfuscator option 1> -mllvm <obfuscator option 2> -mllvm <obfuscator option N> ...
```

### Via `opt` options

Just pass obfuscator options. E.g. `opt -fla -sub ...`



## Obfuscator options

### [Control Flow Flattening](https://github.com/obfuscator-llvm/obfuscator/wiki/Control-Flow-Flattening)

- `fla` - activates control flow flattening
- `split` - activates basic block splitting. Improve the flattening when applied together
- `split_num=3` - if the pass is activated, applies it 3 times on each basic block. Default: 1

### [Substitution](https://github.com/obfuscator-llvm/obfuscator/wiki/Instructions-Substitution)

- `sub` - activate instructions substitution
- `sub_loop=3` - if the pass is activated, applies it 3 times on a function. Default : 1

### [Bogus Control Flow](https://github.com/obfuscator-llvm/obfuscator/wiki/Bogus-Control-Flow)

With this obfuscation may break exception handling!

- `bcf` - activates the bogus control flow pass
- `bcf_loop=3` - if the pass is activated, applies it 3 times on a function. Default: 1
- `bcf_prob=40` - if the pass is activated, a basic bloc will be obfuscated with a probability of 40%. Default: 30

#### String obfuscator

Very simple xor (for llvm15 cesar+xor) string obfuscation. Applied only via command line

- `sobf` - activate string obfuscator pass

# Integrate to Android NDK

### 1. Configure LLVM to add obfuscation support - you can get guide in relate branches

E.g. for Android NDK 24 you must use [release/14.x](https://github.com/sr-tream/obfuscator/tree/release/14.x):

```bash
git clone -b release/14.x https://github.com/sr-tream/obfuscator
cd obfuscator
git submodule update --init llvm-project
cd llvm-project
git apply ../obfuscator.patch
```

### 2. Build clang utils without any libs

```bash
cmake -DCMAKE_BUILD_TYPE=Release -DLLVM_ENABLE_PROJECTS="clang;lld" -DLLVM_ENABLE_LLD=ON -DLLVM_STATIC_LINK_CXX_STDLIB=ON -S llvm -B build
cmake --build build --parallel
```

Also you can use CMake variable ` LLVM_TARGETS_TO_BUILD` to build only for required platforms, e.g. ` -DLLVM_TARGETS_TO_BUILD="ARM"` to build only for **armv7**

### 3. Install clang utils to android-ndk

```bash
cmake --install build --prefix ${ANDROID_NDK}/toolchains/llvm/prebuilt/linux-x86_64/
```

Here `${ANDROID_NDK}` is path to folder with android-ndk

### 4. Copy android libs to use with obfuscated LLVM

```bash
cp -r ${ANDROID_NDK}/toolchains/llvm/prebuilt/linux-x86_64/lib64/clang/${ANDLLVM}/lib ${ANDROID_NDK}/toolchains/llvm/prebuilt/linux-x86_64/lib/clang/${OLLVM}
```

Here:

- `${ANDROID_NDK}` - path to folder with android-ndk
- `${ANDLLVM}` - version of LLVM bundled with android-ndk
- `${OLLVM}` - version of installed LLVM with obfuscation support (selected on paragraph 1)



# Also, you can look [Wiki of original project](https://github.com/obfuscator-llvm/obfuscator/wiki)

## Major difference

### 1. New project tree

Original project use patched copy of LLVM. 

This repo use submodule with LLVM and patch for obfuscation support.

### 2. Work with attribute annotation for functions

Original project doesn't support options `split_num`, `sub_loop`, `bcf_loop` and `bcf_prob` for use in function annotations. This fork support it.

