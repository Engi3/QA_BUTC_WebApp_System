Master Context: QA BUTC System 3.0 (MVC Architecture)
📌 1. Project Identity
    Name: QA BUTC System v3.0
    Platform: Google Apps Script (GAS) / Google Sheets as Database
    Purpose: ระบบฐานข้อมูลและติดตามงานประกันคุณภาพการศึกษา วิทยาลัยเทคนิคบูรพาปราจีน
    Core Principle: Separation of Concerns (SoC) และ Role-Based Access Control (RBAC)

🏗 2. Architecture Logic (MVC Pattern)
ทุกการแก้ไขต้องยึดตามโครงสร้างไฟล์ดังนี้:
Model (Data & Server Logic: Code.gs)
    ใช้ Google Sheets เป็น Database (Tables: tb_users, tb_sections, tb_departments, tb_extra_emails, tb_dashboard, tb_settings).
    จัดการ CRUD ทั้งหมดผ่าน google.script.run.
    Rule: ห้ามเขียนตรรกะการคำนวณ UI ไว้ใน Code.gs ให้ส่งเพียง Raw Data (Object/Array) กลับไปที่ Client.
View (UI Components: .html)
    Main Skeleton: Index.html (Single Page Application).
    Styling: แยกไฟล์ css_base.html (Reset/Vars), css_theme.html (Dynamic Theme), css_patch.html (Fixes).
    Rules: ใช้ Bootstrap 5.3 + Bootstrap Icons. ห้ามเขียน Inline CSS (ให้ใช้ Variables ใน css_base).
Controller (Business Logic: js_*.html)
    js_core: จัดการ Routing (switchView), App Init, และ Global State (APP object).
    js_render: จัดการการวาด Dashboard (Public, Admin, Manager) และ Shared Data Logic.
    js_admin / js_user: แยกตรรกะตามบทบาทผู้ใช้.
    js_utils: Shared Components เช่น Accordion Chips, Toasts, Custom Parsers.
🔐 3. Security & Access Logic (RBAC)
    Admin: สิทธิ์สูงสุด เข้าถึง view-admin-dashboard และจัดการ Metadata ทุกอย่าง.
    Manager: มอบหมายสิทธิ์ผ่านรหัส Department (DEPT_...).
        Logic: ระบบต้องแตก (Resolve) รหัส DEPT ออกเป็น SEC (งานย่อย) ทั้งหมดที่สังกัดอยู่แบบ Real-time เสมอ.
    User: มอบหมายสิทธิ์ผ่านรหัส Section (SEC_...) โดยตรง.
    Whitelist (Extra Login): ตรวจสอบอีเมลจาก tb_extra_emails ก่อนตรวจสอบ @butc.ac.th. ถ้าอยู่ในตารางนี้ ให้ข้ามเงื่อนไขโดเมนองค์กรทันที.
⚙️ 4. Essential Programming Rules (For AI Developers)
    Shared Logic: ก่อนเพิ่มฟังก์ชันใหม่ ให้ตรวจสอบ js_utils.html เสมอ เพื่อลดการเขียนโค้ดซ้ำ (Don't Repeat Yourself - DRY).
    Manager Mapping: ทุกฟังก์ชันที่แสดงผลงานของ Manager (Dashboard, Form List) ต้องเรียกใช้ resolveManagerSections(rawIds) ใน js_render.html เพื่อให้ข้อมูลตรงกันทั้งระบบ.
    Accordion Chips: การเลือกรายการหลายรายการ (Sections/Departments) ต้องใช้ระบบ buildChipGrid ใน js_utils.html เพื่อความเป็นระเบียบและพับเก็บได้.
    IQA Mapping: การ Mapping ข้อมูลระหว่าง Form และ IQA มาตรฐาน ต้องรักษาโครงสร้าง 2-Step (Form -> Field) ใน js_iqa.html.
    State Management: เก็บข้อมูลที่โหลดมาครั้งเดียวไว้ใน APP object เพื่อลดจำนวนการ Call ไปยัง Server.
🛠 5. How to use this with CLI
เมื่อคุณต้องการสั่งงานผ่าน CLI (Claude/Gemini) ให้ใช้คำสั่งเริ่มต้นว่า:
    "Read QA_Master_Prompt.md first to understand the architecture. Then, proceed to [Job Description] in file [File_Name]."
Current Feature Modules:
    [x] Extra Login Support
    [x] Real-time Manager Mapping
    [x] Foldable Section/Dept Selection
    [x] Virtual IQA Display
    [x] Advanced Gradient Theme Builder