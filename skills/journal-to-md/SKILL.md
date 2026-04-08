---
name: journal-to-md
description: Convert academic journals (PDF, Word, PowerPoint, Excel, images, audio) to structured Markdown using Microsoft MarkItDown
---

# Journal to Markdown Converter Skill

## 概述 (Overview)

此 Skill 使用 Microsoft MarkItDown 工具將各種格式的學術文獻轉換為結構化的 Markdown 格式,便於後續整理、閱讀與知識管理。

## 核心功能 (Core Features)

1. **單檔轉換**: 將單一學術文獻轉換為 Markdown
2. **批次轉換**: 一次處理整個資料夾的文獻
3. **智能提取**: 保留文獻結構(標題、段落、列表、表格等)
4. **多格式支援**: PDF、Word (.docx)、PowerPoint (.pptx)、Excel (.xlsx)、圖片 (OCR)、音訊 (轉錄)

## 安裝需求 (Installation Requirements)

首次使用時需要安裝 MarkItDown:

```bash
pip install markitdown
```

## 使用方式 (Usage)

### 1. 單檔轉換

當使用者提供單一檔案路徑時:

```python
from markitdown import MarkItDown

# 初始化轉換器
md = MarkItDown()

# 轉換檔案
result = md.convert("path/to/academic_paper.pdf")

# 儲存為 Markdown
output_path = "path/to/academic_paper.md"
with open(output_path, "w", encoding="utf-8") as f:
    f.write(result.text_content)
```

### 2. 批次轉換

當使用者提供資料夾路徑時:

```python
import os
from pathlib import Path
from markitdown import MarkItDown

def batch_convert(input_folder, output_folder=None):
    """
    批次轉換資料夾中的所有支援格式檔案
    
    Args:
        input_folder: 輸入資料夾路徑
        output_folder: 輸出資料夾路徑(預設為輸入資料夾下的 markdown 子資料夾)
    """
    md = MarkItDown()
    
    # 設定輸出資料夾
    if output_folder is None:
        output_folder = os.path.join(input_folder, "markdown")
    os.makedirs(output_folder, exist_ok=True)
    
    # 支援的檔案格式
    supported_extensions = ['.pdf', '.docx', '.pptx', '.xlsx', '.jpg', '.jpeg', '.png']
    
    # 遍歷資料夾
    converted_files = []
    failed_files = []
    
    for file_path in Path(input_folder).rglob('*'):
        if file_path.suffix.lower() in supported_extensions:
            try:
                # 轉換檔案
                result = md.convert(str(file_path))
                
                # 產生輸出檔名
                relative_path = file_path.relative_to(input_folder)
                output_path = Path(output_folder) / relative_path.with_suffix('.md')
                output_path.parent.mkdir(parents=True, exist_ok=True)
                
                # 儲存 Markdown
                with open(output_path, "w", encoding="utf-8") as f:
                    f.write(result.text_content)
                
                converted_files.append(str(file_path))
            except Exception as e:
                failed_files.append((str(file_path), str(e)))
    
    return converted_files, failed_files
```

### 3. 智能文獻結構提取

轉換後可進一步分析 Markdown 結構,提取關鍵部分:

```python
def extract_paper_sections(markdown_text):
    """
    從轉換後的 Markdown 中提取學術論文的關鍵章節
    
    Returns:
        dict: 包含 abstract, introduction, method, results, discussion 等章節
    """
    sections = {
        'abstract': '',
        'introduction': '',
        'method': '',
        'results': '',
        'discussion': '',
        'references': ''
    }
    
    # 使用正則表達式或簡單字串匹配提取章節
    # 這部分可根據實際轉換結果調整
    
    return sections
```

## 工作流程範例 (Workflow Examples)

### 範例 1: 轉換單篇論文

```
USER: 請將這篇論文轉換成 Markdown
[提供 PDF 路徑]

ASSISTANT:
1. 檢查 markitdown 是否已安裝
2. 使用 MarkItDown 轉換 PDF
3. 儲存為 .md 檔案
4. 回報轉換結果與輸出路徑
```

### 範例 2: 批次處理文獻資料夾

```
USER: 請將這個資料夾裡的所有論文都轉成 Markdown
[提供資料夾路徑]

ASSISTANT:
1. 掃描資料夾中的所有支援格式檔案
2. 批次轉換所有檔案
3. 在原資料夾下建立 markdown 子資料夾
4. 回報轉換統計(成功/失敗數量)
```

### 範例 3: 轉換後同步至 Notion

```
USER: 請將這篇論文轉成 Markdown 並同步到 Notion

ASSISTANT:
1. 轉換 PDF 為 Markdown
2. 提取論文關鍵資訊(標題、作者、摘要等)
3. 使用 Notion MCP 建立新頁面
4. 將 Markdown 內容寫入 Notion
5. 回報 Notion 頁面連結
```

## 輸出格式建議 (Output Format Recommendations)

轉換完成後,建議在 Markdown 檔案開頭加入 YAML frontmatter:

```markdown
---
title: [論文標題]
authors: [作者列表]
year: [發表年份]
journal: [期刊名稱]
doi: [DOI]
converted_date: [轉換日期]
source_file: [原始檔案路徑]
---

# [論文標題]

[轉換後的內容]
```

## 錯誤處理 (Error Handling)

1. **檔案不存在**: 檢查路徑是否正確
2. **不支援的格式**: 提示使用者支援的格式清單
3. **轉換失敗**: 記錄錯誤訊息,建議使用者檢查檔案是否損壞
4. **編碼問題**: 統一使用 UTF-8 編碼儲存

## 進階功能 (Advanced Features)

### 圖片處理

MarkItDown 支援 OCR,可從圖片中提取文字:

```python
# 轉換包含圖片的 PDF 或直接轉換圖片
result = md.convert("screenshot.png")
```

### 音訊轉錄

若學術會議有錄音檔,可轉錄為文字:

```python
# 需要額外安裝語音識別套件
result = md.convert("conference_talk.mp3")
```

## 整合建議 (Integration Recommendations)

1. **與 Notion 整合**: 轉換後自動推送至「學術夥伴研究日誌」
2. **與文獻管理工具整合**: 配合 Zotero、Mendeley 等工具的資料夾結構
3. **建立文獻索引**: 批次轉換後建立全文檢索資料庫
4. **自動化工作流**: 監控特定資料夾,新增檔案時自動轉換

## 注意事項 (Important Notes)

1. **轉換品質**: PDF 品質會影響轉換結果,掃描版 PDF 效果可能較差
2. **格式保留**: 複雜的排版(如多欄位、特殊字體)可能無法完美保留
3. **數學公式**: LaTeX 公式可能需要手動調整
4. **參考文獻**: 建議轉換後檢查參考文獻格式是否正確

## 參考資源 (References)

- [MarkItDown GitHub Repository](https://github.com/microsoft/markitdown)
- [MarkItDown Documentation](https://github.com/microsoft/markitdown?tab=readme-ov-file)
