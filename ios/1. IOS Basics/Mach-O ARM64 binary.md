- typical Mach-O / ARM64 binary
    - **File header / magic & load commands**
        - **What it is:** magic number, CPU type (ARM64), number of load commands, entry point info, offsets to following structures.
        - **Example you’ll see:** a Mach-O header showing `MH_MAGIC_64`, `CPU: ARM64`, `ncmds: 12`.
        - **Tool / command:** `otool -hV <binary>` or `llvm-otool -hv <binary>`. [llvm.org+1](https://llvm.org/docs/CommandGuide/llvm-otool.html?utm_source=chatgpt.com)
    - **Load commands / segment table**
        - **What it is:** a list of load commands that describe how to map the file into memory (segments like `__TEXT`, `__DATA`, `__LINKEDIT`), dynamic libraries, entrypoint, etc.
        - **Example:** `segname __TEXT vmaddr 0x100000000 vmsize 0x4000 fileoff 0x0 filesize 0x2000`.
        - **Tool / command:** `otool -l <binary>` (shows load commands + segments). [yurylapitsky.com+1](https://yurylapitsky.com/exploring_mach-o_binaries?utm_source=chatgpt.com)
            ![[Pasted image 20251003165710.png]]
            **segment table help to split func code**
            ![[Pasted image 20251003165729.png]]
            deleted segment table → all code listed in 1 section
    - **Segment contents (sections)** — stored next according to offsets
        - **What it is:** each segment (`__TEXT`, `__DATA`) contains _sections_ such as `__text` (instructions), `__cstring` (strings), `__const`, `__bss` (conceptually — bss may be virtual-only), `__objc_*` (Objective-C metadata).
        - **Example:** section header: `Section __TEXT,__text addr 0x100000f40 size 0x1b0 offset 0xF40`.
        - **Tool / command:** `otool -l <binary>` to list sections; `otool -tV <binary>` to disassemble `__text`. [yurylapitsky.com+1](https://yurylapitsky.com/exploring_mach-o_binaries?utm_source=chatgpt.com)
    - **Code bytes (the .text section bytes) and other section raw data**
        - **What it is:** raw instruction bytes for functions (.text), raw data for `.rodata`/`.cstring`, etc. These are at the file offsets listed by section headers.
        - **Example:** disassembly snippet from `.text`: `sub sp, sp, #0x20; stp x29, x30, [sp, #0x10]`.
        - **Tool / command:** `llvm-objdump -d <binary>` or `otool -tV <binary>`. [llvm.org+1](https://llvm.org/docs/CommandGuide/llvm-objdump.html?utm_source=chatgpt.com)
    - **Symbol table / string table (often in __LINKEDIT)**
        - **What it is:** entries mapping symbol names → addresses (and the string table holds names). In Mach-O, symbol & string data commonly live inside the `__LINKEDIT` segment.
        - **Example:** `0000000100000f40 T _main` or undefined symbol `U _printf`.
        - **Tool / command:** `nm -n <binary>` (sorted by address) or `llvm-readobj --syms <binary>`; `otool -Iv <binary>` can also show symbol info. [newosxbook.com+1](https://newosxbook.com/index.php?page=downloads&utm_source=chatgpt.com)
    - **Relocations / binding info / GOT / lazy stubs**
        - **What it is:** relocation records and dynamic bind info used by the loader to fix addresses for imported functions/data at load time (PLT/GOT analogs; Mach-O has stubs, lazy pointers, bind/rebase opcodes).
        - **Example:** lazy bind entry pointing to `dyld` resolution for `_objc_msgSend`.
        - **Tool / command:** `xcrun dyldinfo <binary>` or `otool -l` (look for `__la_symbol_ptr`, `__nl_symbol_ptr`, or lazy binding data). [犹落+1](https://dengweijun.com/apple-binary-cli?utm_source=chatgpt.com)
    - **Debug sections (DWARF) — optional / often external**
        - **What it is:** `.debug_info`, `.debug_line`, etc. — detailed debug symbols and source mapping (may be stripped or placed in separate dSYM files).
        - **Example:** `DW_TAG_subprogram` entries, file/line mappings, variable locations.
        - **Tool / command:** `dwarfdump <binary or dSYM>` or `atos` to map addresses to symbols (with dSYM). [newosxbook.com](https://newosxbook.com/index.php?page=downloads&utm_source=chatgpt.com)
    - **Code signature & other trailing blocks**
        - **What it is:** iOS/macOS code signature, entitlement blobs, resources appended by the OS signing tools. Usually near end of file or in special load command.
        - **Example:** `LC_CODE_SIGNATURE` load command with offset/size.
        - **Tool / command:** `codesign -d --verbose=4 <binary>` or `otool -l` to view `LC_CODE_SIGNATURE`. [yurylapitsky.com](https://yurylapitsky.com/exploring_mach-o_binaries?utm_source=chatgpt.com)