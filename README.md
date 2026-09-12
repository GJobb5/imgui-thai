# Dear ImGui - Thai Language Edition (ภาษาไทย) 🇹🇭

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE.txt)
[![Dear ImGui](https://img.shields.io/badge/Dear%20ImGui-v1.92+-orange.svg)](https://github.com/ocornut/imgui)
[![Platform](https://img.shields.io/badge/Platform-Windows%20%7C%20Linux%20%7C%20macOS-lightgrey.svg)]()

A fork of [ocornut/imgui](https://github.com/ocornut/imgui) with **first-class, native Thai language support** — including automatic typography shaping (tone mark lifting over upper vowels), tall consonant ascender offsets, seamless Windows keyboard input (TIS-620/CP874 to Unicode auto-mapping), glyph range helpers, and word-wrap cluster protection.

---

## ทำไมต้อง imgui-thai? (Why imgui-thai?)

Dear ImGui ดั้งเดิมไม่มีระบบ OpenType GPOS/GSUB ทำให้เมื่อนำไปแสดงผลหรือพิมพ์ภาษาไทยจะพบปัญหาหลักดังนี้:

| ปัญหาใน ImGui ดั้งเดิม ❌ | แก้ไขแล้วใน imgui-thai ✅ |
| :--- | :--- |
| **วรรณยุกต์จมทับสระบน**: คำอย่าง `ตั้งใจ`, `ปิ๊ง`, `ที่นี่`, `ขึ้น`, `ตั๋ว` ไม้เอก/โท/ตรี/จัตวา จะจมลงไปทับซ้อนกับ สระอิ/อี/อึ/อือ/ไม้หันอากาศ จนอ่านไม่ออก | **Automatic Tone Mark Lifting**: ระบบจะยกวรรณยุกต์ขึ้นเหนือสระบนด้วย Golden Ratio (`3.0px * scale`) อัตโนมัติ วรรณยุกต์วางตัวชิดสวยงามพอดีเป๊ะ |
| **สระและวรรณยุกต์ชนหางพยัญชนะ**: บนตัวอักษรหางยาว เช่น `ป`, `ฝ`, `ฟ`, `ฬ` สระบนและวรรณยุกต์จะทับเส้นหาง | **Tall Consonant Offset**: สระบนและวรรณยุกต์จะถูกเยื้องหลบซ้าย (`-1.5px * scale`) อัตโนมัติเมื่อวางอยู่บนพยัญชนะหางยาว |
| **แป้นพิมพ์ภาษาไทยเพี้ยนบน Windows**: หน้าต่าง ANSI หรือเครื่องที่ตั้ง Locale เป็นภาษาอังกฤษ การพิมพ์ไทยจะถูกแปลงเป็นตัวอักษรละตินเพี้ยน (`¡`, `¢`, `£`) | **Win32 Input Translation**: ดักจับ `WM_CHAR` และแปลงรหัส TIS-620/CP874 (`0xA1`-`0xFB`) สู่ Unicode อัตโนมัติ พิมพ์ไทยได้ทันที 100% |
| **การตัดบรรทัด (Word Wrap) แยกสระ/วรรณยุกต์**: สระบนหรือวรรณยุกต์อาจถูกตัดหลุดไปอยู่ต้นบรรทัดถัดไปตัวเดียวโดดๆ | **Word-Wrap Cluster Protection**: ป้องกันไม่ให้ระบบตัดคำแบ่งแยกสระและวรรณยุกต์ออกจากพยัญชนะต้น |
| **Zero Overhead**: ภาษาอื่นและอักขระ ASCII ทั่วไปยังคงทำงานด้วยความเร็วสูงสุดเท่าเดิม ไม่เสีย Performance |

---

## วิธีใช้งาน (Quick Start)

### 1. โหลด Font ภาษาไทย
โหลดฟอนต์ภาษาไทย TrueType/OpenType (เช่น Prompt, Kanit, Sarabun, Tahoma, Leelawadee) พร้อมระบุ `GetGlyphRangesThai()`:

```cpp
#include "imgui.h"

ImGuiIO& io = ImGui::GetIO();

// โหลดฟอนต์ภาษาไทยขนาด 18px พร้อมช่วงตัวอักษรภาษาไทย (0x0E00 - 0x0E7F)
io.Fonts->AddFontFromFileTTF("fonts/Prompt-Regular.ttf", 18.0f, nullptr, io.Fonts->GetGlyphRangesThai());
```

### 2. แสดงผลข้อความภาษาไทย (Text Rendering)
สามารถใช้ UTF-8 string literals (`u8"..."`) แสดงผลได้ทันที:

```cpp
ImGui::Begin(u8"หน้าต่างทดสอบภาษาไทย");

ImGui::Text(u8"สวัสดีครับ ยินดีต้อนรับสู่ Dear ImGui ภาษาไทย!");

// ทดสอบคำที่ซ้อนวรรณยุกต์และสระบน
ImGui::BulletText(u8"วรรณยุกต์บนสระ: ตั้งใจ, ที่นี่, ขึ้น, พื้น, ตั๋ว");
ImGui::BulletText(u8"พยัญชนะหางยาว: ปิ๊ง, ผู้ใหญ่, ฝรั่ง, ฟื้นฟู");
ImGui::BulletText(u8"ตัวเลขไทย: ๐๑๒๓๔๕๖๗๘๙");

ImGui::End();
```

### 3. กล่องพิมพ์ข้อความภาษาไทย (InputText)
รองรับการสลับคีย์บอร์ดไทยและพิมพ์ใน `ImGui::InputText()` ได้ทันที ไม่ต้องเขียนโค้ด Hook เพิ่มเติม:

```cpp
static char inputBuffer[256] = u8"ทดสอบพิมพ์ข้อความ";
ImGui::InputText(u8"พิมพ์ภาษาไทย", inputBuffer, sizeof(inputBuffer));
```

---

## การรวมเข้ากับโปรเจกต์ (CMake Integration)

คุณสามารถดึง `imgui-thai` ไปใช้งานผ่าน `FetchContent` ในไฟล์ `CMakeLists.txt` ของโปรเจกต์คุณได้ง่ายๆ:

```cmake
include(FetchContent)

FetchContent_Declare(
    imgui
    GIT_REPOSITORY https://github.com/GJobb5/imgui-thai.git
    GIT_TAG master
)
FetchContent_MakeAvailable(imgui)

# ลิงก์และ Include เข้ากับ Target ของคุณ
target_include_directories(YourTarget PRIVATE ${imgui_SOURCE_DIR})
target_sources(YourTarget PRIVATE
    ${imgui_SOURCE_DIR}/imgui.cpp
    ${imgui_SOURCE_DIR}/imgui_draw.cpp
    ${imgui_SOURCE_DIR}/imgui_tables.cpp
    ${imgui_SOURCE_DIR}/imgui_widgets.cpp
    ${imgui_SOURCE_DIR}/backends/imgui_impl_win32.cpp
    ${imgui_SOURCE_DIR}/backends/imgui_impl_dx9.cpp # หรือ backend อื่นที่ต้องการ
)
```

---

## ตัวอย่างใน Demo Window

สามารถเปิดดูตัวอย่างการใช้งานภาษาไทยจริงได้ใน `ImGui::ShowDemoWindow()` โดยไปที่เมนู:
> **Widgets ➔ Text ➔ Thai Text (ภาษาไทย)**

ภายในจะมีตัวอย่างประโยคทดสอบคำยาก และกล่อง Interactive Input ให้ทดลองพิมพ์ภาษาไทยจริง

---

## เอกสารที่เกี่ยวข้อง (Documentation)

- [docs/FONTS.md](docs/FONTS.md) - คำแนะนำการโหลดฟอนต์ การรวมฟอนต์ (Font Merging) และการใช้งาน Glyph Ranges ภาษาไทย
- [docs/README.md](docs/README.md) - เอกสารดั้งเดิมของ Dear ImGui

---

## License & Credits

- **Dear ImGui**: พัฒนาโดย [Omar Cornut](https://github.com/ocornut) ภายใต้ลิขสิทธิ์ [MIT License](LICENSE.txt)
- **Thai Typography & Input Enhancements**: พัฒนาโดย [GJobb5](https://github.com/GJobb5)
