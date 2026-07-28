---
name: docwen-file-converter
description: 公文文档格式转换工具，对接本地 DocWenCLI 命令行工具 (D:\Softwave\DocWen-windows-x64\DocWenCLI.exe)。支持文档/表格/图片/版式文件的格式互转、文档校对(validate)、PDF合并(merge-pdfs)、PDF拆分(split-pdf)、图片合并TIFF(merge-images-to-tiff)等操作。适用于中文公文处理场景：DOCX转PDF、MD转DOCX、PDF转图片、OFD/WPS等国产格式转换、发票OFD/PDF结构化提取。当用户需要文档格式转换、文档校对、PDF操作时使用此skill。
---

# DocWen File Converter

对接 `D:\Softwave\DocWen-windows-x64\DocWenCLI.exe` 的命令行封装。工具路径固定，直接调用即可。

## 基本用法

```powershell
& "D:\Softwave\DocWen-windows-x64\DocWenCLI.exe" <command> [options] <files>
```

常用选项:
- `--to FORMAT` — 目标格式
- `--json` — JSON 输出（适合AI/脚本解析）
- `--batch` — 批量模式
- `--extract-img` — 提取图片
- `--ocr` — OCR识别图片文字
- `--optimize-for gongwen` — 公文优化（DOCX→MD）
- `--optimize-for invoice_cn` — 发票优化（PDF/OFD→MD）
- `--yes` — 跳过确认
- `--continue-on-error` — 出错继续

## 支持格式

| 源类型 | 可转格式 |
|--------|----------|
| document (doc/docx/wps/odt/ofd/rtf) | doc, docx, md, odt, ofd, pdf, rtf, wps |
| image (jpg/png/tif) | md, pdf |
| layout (版式文件) | doc, docx, jpg, md, odt, pdf, png, rtf, tif |
| markdown | csv, doc, docx, ods, odt, rtf, xls, xlsx |
| spreadsheet (xls/xlsx/et/ods/csv) | csv, et, md, ods, pdf, xls, xlsx |

## 常用命令

### 格式转换

```powershell
# DOCX → PDF
& "D:\Softwave\DocWen-windows-x64\DocWenCLI.exe" convert --to pdf document.docx

# DOCX → MD (含图片提取)
& "D:\Softwave\DocWen-windows-x64\DocWenCLI.exe" convert --to md document.docx --extract-img

# PDF → MD (含OCR)
& "D:\Softwave\DocWen-windows-x64\DocWenCLI.exe" convert --to md document.pdf --ocr

# MD → DOCX (使用模板)
& "D:\Softwave\DocWen-windows-x64\DocWenCLI.exe" convert --to docx document.md --template template_name

# 公文优化：DOCX转MD（保留公文格式）
& "D:\Softwave\DocWen-windows-x64\DocWenCLI.exe" convert --to md gongwen.docx --optimize-for gongwen

# 发票提取：PDF/OFD发票转MD
& "D:\Softwave\DocWen-windows-x64\DocWenCLI.exe" convert --to md invoice.pdf --optimize-for invoice_cn

# 批量转换所有docx
& "D:\Softwave\DocWen-windows-x64\DocWenCLI.exe" convert --to pdf *.docx --batch
```

### 文档校对

```powershell
& "D:\Softwave\DocWen-windows-x64\DocWenCLI.exe" validate document.docx
```

### PDF操作

```powershell
# 合并PDF
& "D:\Softwave\DocWen-windows-x64\DocWenCLI.exe" merge-pdfs 1.pdf 2.pdf 3.pdf --output merged.pdf

# 拆分PDF
& "D:\Softwave\DocWen-windows-x64\DocWenCLI.exe" split-pdf document.pdf
```

### 图片操作

```powershell
# 合并多张图片为TIFF
& "D:\Softwave\DocWen-windows-x64\DocWenCLI.exe" merge-images-to-tiff img1.jpg img2.png --output combined.tif
```

### MD标题序号

```powershell
& "D:\Softwave\DocWen-windows-x64\DocWenCLI.exe" md-numbering document.md
```

### 查询能力

```powershell
# 查看文件支持的操作
& "D:\Softwave\DocWen-windows-x64\DocWenCLI.exe" inspect document.docx

# 列出所有可用操作
& "D:\Softwave\DocWen-windows-x64\DocWenCLI.exe" actions

# 列出可用模板
& "D:\Softwave\DocWen-windows-x64\DocWenCLI.exe" templates

# 列出可用序号方案
& "D:\Softwave\DocWen-windows-x64\DocWenCLI.exe" numbering-schemes
```

## 从DOCX提取照片

DOCX中的嵌入图片可通过 Python + Pillow 提取：

```powershell
python -c "
import docx, os
from PIL import Image
import io

doc = docx.Document('input.docx')
for rel in doc.part.rels.values():
    if 'image' in rel.reltype:
        img_data = rel.target_part.blob
        img = Image.open(io.BytesIO(img_data))
        if img.mode == 'RGBA':
            img = img.convert('RGB')
        img.save('output.jpg', 'JPEG', quality=92)
        break  # first image only
"
```

结合批量处理可依次提取所有DOCX文件中的照片。

## 注意事项

- 工具路径固定为 `D:\Softwave\DocWen-windows-x64\DocWenCLI.exe`
- 首次调用会加载配置（约2-3秒），之后调用更快
- 工具网络隔离，完全离线运行
- 中文界面，无需额外语言配置
- JSON 输出模式 (`--json`) 适合程序化调用
