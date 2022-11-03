
## 约定

u8 为一个字节，8bits。

u32_leb 32位为一个字的LEB无符号变长编码。

## 一些知识

wasm的长度采用LEB编码(Little Endian Base 128)，是一种变长编码。最高位表示后面是否继续读取，其他7位表示数值。

例如，```0xEA 0x01 0x0B``` 转化为二进制 ```0b11101010 0b00000001``` 。0xEA最高位为1，表示后面还有数字。所以长度为106+128*1=234。

## 开始阅读

wasm字节流的解析入口：[https://github.com/myvyang/wasm_source_analysis/blob/main/v8_source/src/api/api.cc#L2590](https://github.com/myvyang/wasm_source_analysis/blob/main/v8_source/src/api/api.cc#L2590)

对module进行解析：https://source.chromium.org/chromium/chromium/src/+/main:v8/src/wasm/module-decoder-impl.h;drc=44798fcb6d921c4a8fee8331289021a3b25b4450;l=1641

## 输入字节解析

### DecodeModuleHeader 

u32 魔数(\x00asm)

u32 版本号

### WasmSectionIterator::next

u8 段类型，定义在 https://source.chromium.org/chromium/chromium/src/+/main:v8/src/wasm/wasm-constants.h;drc=44798fcb6d921c4a8fee8331289021a3b25b4450;l=95

u32_leb 段长度

### DecodeSection

上一步已经拿到了段的类型和长度，这个函数里根据不同的段类型，分别解析数据。

### DecodeCodeSection

这里解析代码段的数据后，会生成对应的`FunctionBody`对象，后面该对象会在 https://source.chromium.org/chromium/chromium/src/+/main:v8/src/wasm/baseline/liftoff-compiler.cc;l=7735;bpv=1;bpt=0 直接编译成机器码。

## 字节码逻辑

wasm使用字节码，每个bytecode是一个字节。

wasm只支持4种数值类型：i32, i64, f32, f6。

wasm是严格的栈虚拟机。例如```i32.add```指令，从栈顶弹出两个量，相加后压入栈顶。每个指令的动作都是规定好的，因此可以直接验证整个指令序列的合法性。

wasm支持内存读写。内存实际是一个字节数组。

## 编译

ExecuteLiftoffCompilation 里对字节码做了处理，然后调用```compiler->GetCode(&result.code_desc);```编译代码，这里会调用不同的后端，编译成不同平台的指令。

