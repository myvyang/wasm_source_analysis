
## 约定

u8 为一个字节，8bits。

## 开始阅读

wasm字节流的解析入口：[https://github.com/myvyang/wasm_source_analysis/blob/main/v8_source/src/api/api.cc#L2590](https://github.com/myvyang/wasm_source_analysis/blob/main/v8_source/src/api/api.cc#L2590)

对module进行解析：https://source.chromium.org/chromium/chromium/src/+/main:v8/src/wasm/module-decoder-impl.h;drc=44798fcb6d921c4a8fee8331289021a3b25b4450;l=1641

## 输入字节解析

### DecodeModuleHeader 

u32 魔数(\x00asm)

u32 版本号

### WasmSectionIterator::next

u8 段类型，定义在 https://source.chromium.org/chromium/chromium/src/+/main:v8/src/wasm/wasm-constants.h;drc=44798fcb6d921c4a8fee8331289021a3b25b4450;l=95
u32 段长度
