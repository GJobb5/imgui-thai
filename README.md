# Dear ImGui - Thai Language Edition (ImGui ภาษาไทย) 🇹🇭

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE.txt)
[![Dear ImGui](https://img.shields.io/badge/Dear%20ImGui-v1.92+-orange.svg)](https://github.com/ocornut/imgui)
[![Thai Language](https://img.shields.io/badge/Thai%20Language-Supported-brightgreen.svg)]()
[![Platform](https://img.shields.io/badge/Platform-Windows%20%7C%20DirectX%20%7C%20OpenGL%20%7C%20Vulkan-lightgrey.svg)]()
[![GitHub Stars](https://img.shields.io/github/stars/GJobb5/imgui-thai?style=social)](https://github.com/GJobb5/imgui-thai)

**Dear ImGui ภาษาไทย** — A fork of [ocornut/imgui](https://github.com/ocornut/imgui) with **first-class, native Thai language support**. แก้ปัญหา**วรรณยุกต์จมทับสระบน** (เช่น `ตั้ง`, `ปิ๊ง`, `ที่นี่`), สระบนชนหางพยัญชนะ (`ป`, `ฝ`, `ฟ`, `ฬ`), พิมพ์ภาษาไทยไม่ได้ใน Windows (Win32 Keyboard Input TIS-620/CP874 ➔ UTF-8), และการตัดคำ (Word Wrap) แยกสระ/วรรณยุกต์

> **Keywords**: `imgui thai`, `imgui ภาษาไทย`, `dear imgui thai font`, `imgui วรรณยุกต์จม`, `imgui พิมพ์ไทยไม่ได้`, `imgui thai input`, `imgui samp thai`, `tis-620 to utf-8 imgui`, `thai typography shaping`

---

## สารบัญ (Table of Contents)
- [ทำไมต้อง imgui-thai? (Why imgui-thai?)](#ทำไมต้อง-imgui-thai-why-imgui-thai)
- [เปรียบเทียบการแสดงผล (Visual Comparison)](#เปรียบเทียบการแสดงผล-visual-comparison)
- [ฟีเจอร์เด่น (Key Features)](#ฟีเจอร์เด่น-key-features)
- [วิธีเริ่มต้นใช้งาน (Quick Start)](#วิธีเริ่มต้นใช้งาน-quick-start)
- [การติดตั้งผ่าน CMake (FetchContent)](#การติดตั้งผ่าน-cmake-fetchcontent)
- [คำถามที่พบบ่อย & การแก้ปัญหา (FAQ & Troubleshooting)](#คำถามที่พบบ่อย--การแก้ปัญหา-faq--troubleshooting)
- [ตัวอย่างใน Demo Window](#ตัวอย่างใน-demo-window)
- [License & เครดิต](#license--เครดิต)

---

## ทำไมต้อง imgui-thai? (Why imgui-thai?)

Dear ImGui ดั้งเดิมของ ocornut ถูกออกแบบมาเป็น ASCII / Latin-first และไม่มีตัวคำนวณ OpenType GPOS/GSUB ในตัว ทำให้เมื่อนักพัฒนาเกมและแอปพลิเคชัน C++ นำไปแสดงผลภาษาไทย จะพบปัญหาหนัก 4 ประการ:

| ปัญหาใน ImGui ดั้งเดิม ❌ | สิ่งที่ได้รับการแก้ไขใน imgui-thai ✅ |
| :--- | :--- |
| **วรรณยุกต์จมทับสระบน (Tone Mark Sinking)**:<br>คำที่มีสระบนซ้อนวรรณยุกต์ เช่น `ตั้งใจ`, `ปิ๊ง`, `ที่นี่`, `ขึ้น`, `ตั๋ว` ไม้เอก/โท/ตรี/จัตวา/การันต์ จะจมลงไปทับซ้อนกับ สระอิ/อี/อึ/อือ/ไม้หันอากาศ จนอ่านไม่ออก | **Automatic Tone Mark Lifting**:<br>ระบบคำนวณและยกวรรณยุกต์ลอยขึ้นเหนือสระบนด้วยระยะ **`3.0px * scale` (Golden Ratio)** อัตโนมัติ วรรณยุกต์วางตัวสวยงามพอดีเป๊ะ ไม่ลอยสูงและไม่จมทับ |
| **สระและวรรณยุกต์ชนหางพยัญชนะ (Ascender Collision)**:<br>บนพยัญชนะหางยาว เช่น `ป`, `ฝ`, `ฟ`, `ฬ` สระบนและวรรณยุกต์จะถูกวาดทับเส้นหาง | **Tall Consonant Offset**:<br>เยื้องสระบนและวรรณยุกต์หลบซ้าย (`-1.5px * scale`) อัตโนมัติเมื่อวางอยู่บนพยัญชนะหางยาว |
| **พิมพ์ภาษาไทยไม่ได้บน Windows (Win32 Keyboard Bug)**:<br>ในโปรแกรมหรือเกม DirectX/OpenGL (เช่น GTA:SA, SA-MP, Custom Engine) เมื่อกดแป้นพิมพ์ภาษาไทย รหัส ANSI (TIS-620 `0xA1`-`0xFB`) จะถูกแปลงผิดเป็นตัวอักษรละตินเพี้ยน (`¡`, `¢`, `£`) | **Win32 Input Translation**:<br>ตรวจจับข้อความ `WM_CHAR` และแปลงรหัส TIS-620 / CP874 เป็น Unicode ภาษาไทยทันที 100% พิมพ์ไทยได้ทั้งบนหน้าต่าง Unicode และ ANSI ไม่ขึ้นกับ System Locale |
| **การตัดบรรทัด (Word Wrap) แยกสระ/วรรณยุกต์**:<br>เมื่อข้อความยาวเกินกรอบ สระหรือวรรณยุกต์อาจถูกตัดหลุดไปอยู่ต้นบรรทัดถัดไปตัวเดียวโดดๆ | **Word-Wrap Cluster Protection**:<br>ป้องกันไม่ให้ระบบตัดคำแบ่งแยกสระบน/ล่างและวรรณยุกต์ออกจากพยัญชนะต้น |
| **ประสิทธิภาพ (Zero Performance Overhead)**:<br>การตรวจสอบทำด้วย Bitwise / Fast Range Checks จึงไม่มี Overhead ต่อภาษาอื่น | **100% Backward Compatible**:<br>เข้ากันได้กับ Dear ImGui v1.92+ ดั้งเดิมทุกประการ |

---

## เปรียบเทียบการแสดงผล (Visual Comparison)

```
[ImGui ดั้งเดิม]             [imgui-thai (รุ่นนี้)]
-----------------------------------------------------------
     ั้                         ้ 
     ตง                       ั
                             ตง
 (ไม้โทจมทับไม้หันอากาศ)       (ไม้โทลอยอยู่เหนือไม้หันอากาศพอดีเป๊ะ)
```

---

## วิธีเริ่มต้นใช้งาน (Quick Start)

### 1. โหลดฟอนต์ภาษาไทย (Font Loading)
คุณสามารถใช้ฟอนต์ภาษาไทย TrueType / OpenType ใดก็ได้ เช่น **Prompt**, **Kanit**, **Sarabun**, **Tahoma**, **Leelawadee UI**:

```cpp
#include "imgui.h"

ImGuiIO& io = ImGui::GetIO();

// โหลดฟอนต์ภาษาไทยขนาด 18px พร้อมช่วงตัวอักษรภาษาไทย (0x0E00 - 0x0E7F)
io.Fonts->AddFontFromFileTTF("fonts/Prompt-Regular.ttf", 18.0f, nullptr, io.Fonts->GetGlyphRangesThai());
```

### 2. แสดงผลข้อความภาษาไทย (Text Rendering)
ใช้สตริง UTF-8 (`u8"..."`) ได้ทันที:

```cpp
ImGui::Begin(u8"ระบบเมนูภาษาไทย");

ImGui::Text(u8"ยินดีต้อนรับสู่โปรแกรมของเรา!");

// ทดสอบคำซ้อนวรรณยุกต์
ImGui::BulletText(u8"คำทดสอบสระบนซ้อนวรรณยุกต์: ตั้งใจ, ปิ๊ง, ที่นี่, ขึ้น, พื้น, ตั๋ว");
ImGui::BulletText(u8"คำทดสอบพยัญชนะหางยาว: ผู้ใหญ่, ปฏิบัติ, ฝรั่งเศส, ฟื้นฟู");
ImGui::BulletText(u8"ตัวเลขไทย: ๐ ๑ ๒ ๓ ๔ ๕ ๖ ๗ ๘ ๙");

ImGui::End();
```

### 3. กล่องรับข้อความภาษาไทย (InputText)
รองรับการพิมพ์ภาษาไทย สลับภาษาด้วย `~` (Grave Accent) หรือ Alt+Shift ได้อย่างราบรื่น:

```cpp
static char textBuffer[256] = u8"ข้อความภาษาไทย";
ImGui::InputText(u8"ค้นหาข้อมูล", textBuffer, sizeof(textBuffer));
```

---

## การติดตั้งผ่าน CMake (FetchContent)

เพิ่มโค้ดนี้ลงใน `CMakeLists.txt` เพื่อดึง `imgui-thai` ไปใช้ในโปรเจกต์ของคุณอัตโนมัติ:

```cmake
include(FetchContent)

FetchContent_Declare(
    imgui
    GIT_REPOSITORY https://github.com/GJobb5/imgui-thai.git
    GIT_TAG master
)
FetchContent_MakeAvailable(imgui)

# เพิ่ม Include Path และ Source Files
target_include_directories(YourGameOrApp PRIVATE ${imgui_SOURCE_DIR})
target_sources(YourGameOrApp PRIVATE
    ${imgui_SOURCE_DIR}/imgui.cpp
    ${imgui_SOURCE_DIR}/imgui_draw.cpp
    ${imgui_SOURCE_DIR}/imgui_tables.cpp
    ${imgui_SOURCE_DIR}/imgui_widgets.cpp
    ${imgui_SOURCE_DIR}/backends/imgui_impl_win32.cpp
    ${imgui_SOURCE_DIR}/backends/imgui_impl_dx9.cpp    # หรือ backend แสดงผลของคุณ (dx11, dx12, opengl3, vulkan)
)
```

---

## คำถามที่พบบ่อย & การแก้ปัญหา (FAQ & Troubleshooting)

### Q: ทำไมตัวหนังสือภาษาไทยยังขึ้นเป็นเครื่องหมายคำถาม (?) หรือสี่เหลี่ยม?
**A:** เกิดจากยังไม่ได้โหลดฟอนต์ภาษาไทย หรือไม่ได้ใส่ช่วงตัวอักษร `io.Fonts->GetGlyphRangesThai()`. ฟอนต์เริ่มต้น (ProggyClean) ของ ImGui รองรับเฉพาะภาษาอังกฤษเท่านั้น ให้ใช้คำสั่ง `io.Fonts->AddFontFromFileTTF("font.ttf", 18.0f, nullptr, io.Fonts->GetGlyphRangesThai());`

### Q: นำไปใช้กับ SA-MP (San Andreas Multiplayer) หรือ GTA:SA ได้ไหม?
**A:** ได้ 100%! โค้ดในส่วน `imgui_impl_win32.cpp` ถูกออกแบบมาให้รองรับหน้าต่าง ANSI ของ GTA:SA และตัวเกมยุค DirectX 9 โดยเฉพาะ สามารถพิมพ์ภาษาไทยในช่องแชทหรือเมนู UI ได้อย่างสมบูรณ์

### Q: ใช้กับ DirectX 9, DirectX 11, DirectX 12, OpenGL, Vulkan ได้ไหม?
**A:** ได้ทุก Backend! เพราะระบบจัดระดับตัวอักษรไทย (Thai Shaping) ถูกทำในระดับแกนหลัก (`imgui_draw.cpp`) ในขั้นตอนสร้าง Vertex Quad จึงใช้งานได้กับ Renderer ทุกตัวในโลก

### Q: การตั้งค่าระยะยกวรรณยุกต์ (3.0px) มาจากไหน?
**A:** ผ่านการทดสอบและวิจัยจากขนาด Pixel Matrix จริงของฟอนต์ภาษาไทยยอดนิยม (Prompt, Kanit, Sarabun) ที่ความละเอียด 16px - 24px พบว่าระยะ `3.0px` เป็น Golden Ratio ที่ทำให้วรรณยุกต์วางตัวแนบชิดเหนือไม้หันอากาศ/สระบนพอดี ไม่ทับซ้อน และไม่ลอยสูงหลุดบรรทัด

---

## ตัวอย่างใน Demo Window

คุณสามารถทดสอบภาษาไทยในหน้าต่าง Demo ได้โดยเรียก `ImGui::ShowDemoWindow()` และไปที่เมนู:
> **Widgets ➔ Text ➔ Thai Text (ภาษาไทย)**

---

## License & Credits

- **Dear ImGui**: พัฒนาโดย [Omar Cornut](https://github.com/ocornut) ภายใต้สัญญาอนุญาต [MIT License](LICENSE.txt)
- **Thai Typography Shaping & Win32 Input Translation**: พัฒนาและดูแลโดย [GJobb5](https://github.com/GJobb5)

---

### Tags & Keywords
`imgui-thai` · `dear-imgui-thai` · `imgui-ภาษาไทย` · `thai-font` · `thai-typography` · `thai-shaping` · `gta-sa-thai` · `samp-thai` · `directx-thai` · `win32-tis620` · `cpp-gui-thai`
