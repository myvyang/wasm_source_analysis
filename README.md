
## 约定

u8 为一个字节，8bits。

## 开始阅读

wasm字节流的解析入口：[https://github.com/myvyang/wasm_source_analysis/blob/main/v8_source/src/api/api.cc#L2590](https://github.com/myvyang/wasm_source_analysis/blob/main/v8_source/src/api/api.cc#L2590)

对module进行解析：[https://source.chromium.org/chromium/chromium/src/+/main:v8/src/wasm/module-decoder-impl.h;drc=44798fcb6d921c4a8fee8331289021a3b25b4450;l=1641](https://source.chromium.org/chromium/chromium/src/+/main:v8/src/wasm/module-decoder-impl.h;drc=44798fcb6d921c4a8fee8331289021a3b25b4450;l=1641)

## DecodeModule

DecodeModuleHeader 里，先读了两次 u32 ，解析 魔数 和 版本号 。

然后不停的读段：

```
    WasmSectionIterator section_iter(&decoder, tracer_);

    while (ok()) {
      // Shift the offset by the section header length
      offset += section_iter.payload_start() - section_iter.section_start();
      if (section_iter.section_code() != SectionCode::kUnknownSectionCode) {
        DecodeSection(section_iter.section_code(), section_iter.payload(),
                      offset, validate_functions);
      }
      // Shift the offset by the remaining section payload
      offset += section_iter.payload_length();
      if (!section_iter.more() || !ok()) break;
      section_iter.advance(true);
    }
```

动作实际上靠 WasmSectionIterator::next 推进。
