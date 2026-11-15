# Alexandria Books → Vector DB Pipeline

Turns three Arabic books about Alexandria into source-attributed chunks that fit your
`knowledge_base` spreadsheet and load straight into a vector database (ChromaDB) for a chatbot.

## Important: the books are scanned images

All three PDFs are page images with **no text layer** (one is also copy-protected). Normal text
extraction returns nothing, so the pipeline runs **Arabic OCR (Tesseract)** first. Expect
~1,260 pages in total, so OCR takes a while (it is resumable: finished pages are cached).

## Setup

```bash
# Ubuntu / Debian
sudo apt install poppler-utils tesseract-ocr tesseract-ocr-ara
pip install -r requirements.txt
```

Put the three files in `./books/` (keep the original file names; the zip is unpacked automatically).

## Run

```bash
python extract_books.py ocr   --books-dir ./books --workdir ./work   # 1. OCR every page (slow)
python extract_books.py build --workdir ./work --out ./out           # 2. clean + chunk + export
python extract_books.py index --out ./out --db ./chroma_db           # 3. embeddings -> ChromaDB
python extract_books.py query "من أسس مدينة الإسكندرية؟" --db ./chroma_db   # 4. test search
# or everything at once:
python extract_books.py all --books-dir ./books
```

Tip: run `ocr --only alex-modern-history` first (153 pages) to check quality before the big books.

## Outputs (`./out`)

| File | Use |
|---|---|
| `chunks.jsonl` | One record per chunk with all fields, for any vector DB |
| `knowledge_base_books.xlsx` | Same layout as your sheet: header row, hint row, blank row, data from row 4 |
| `chunks.csv` | Same data as CSV (UTF-8 with BOM, opens correctly in Excel) |
| `../chroma_db/` | Persistent ChromaDB collection `alexandria_books` |

## Fields (matching your sheet)

| Field | Value for the books |
|---|---|
| `language` | `ar` |
| `source_name` | `كتاب: <title> – <author> – ص <pages> (PDF)` |
| `source_url` | `book://<book_id>#page=<n>` (books have no website, so this is an internal reference) |
| `license` | `حقوق محفوظة – استخدام داخلي (تحقق من الحقوق)` (**not** CC-BY-SA like your Wikipedia rows) |
| `date_retrieved` | the day you run `build` |
| `confidence` | from OCR quality: `عالية` (≥80), `متوسطة` (65–80), `تحتاج مراجعة` (<65) |
| `reviewer` | empty |

Extra metadata stored in the vector DB for filtering and citations: `book_id`, `book_title`,
`book_title_en`, `author`, `period`, `source_file`, `page_start`, `page_end`, `ocr_conf`.

**Things to check on your side**

1. Your screenshot showed only columns E–K. Columns A–D here (`id`, `topic`, `text`, `page_ref`)
   are my guess. Edit the `COLUMNS` list at the top of `extract_books.py` to match your real headers.
2. Page numbers are **PDF page numbers**, which can differ from the printed numbers.
3. Fill in `author`, `translator`, `publisher`, `year` in `books_metadata.json`. I could only
   confirm the author of book 3 from its preface. Source names will include them automatically.
4. The books are copyrighted works. Check you have the right to put them in a chatbot before publishing.
5. Chunks flagged `تحتاج مراجعة` are the ones to proofread first (the modern-history scan is the weakest).

## The three books

### 1. الإسكندرية: أعظم عواصم العالم القديم (`alex-greatest-ancient-capital`)
378 pages, a translated work (the contents list has translator, reviewer and author introductions),
published in the series *مصريات: تاريخ – فن – حضارة*. A narrative history of ancient Alexandria
in three chapters:

- **Ch. 1: Capital of the Ptolemaic kingdom, 331–30 BC.** Alexander's dream and the founding of the city,
  its legends, the Ptolemies and their court, a multi-ethnic city, the great harbour and Pharos
  ("the key to Egypt"), Isis and Serapis, science, Homer and the library, ending with Cleopatra.
- **Ch. 2: Roman province and capital, 30 BC–284 AD.** Caesar's visit, the Jewish community and its unrest,
  Vespasian, Serapis under Severus, and the question of Alexandria becoming Christian.
- **Ch. 3: Seat of the Patriarchs, 284–641 AD.** The Christian communities, the Arian controversy and
  Athanasius, the 365 earthquake, the fall of the Serapeum, Hypatia, the Council of Chalcedon,
  and the Arab conquest of 641.
- **Back matter:** references, currency values, author/translator biographies.

### 2. تاريخ الإسكندرية في العصر الحديث (`alex-modern-history`)
153 pages, scan from al-mostafa.com (the site banner is stripped automatically). A short history
of the city from the French campaign onward. The introduction reviews the sources used. Chapters:
the city's condition when the French campaign arrived, the first British occupation, a chapter
on Mamluk-era rule (title partly illegible in the scan), the Fraser campaign, the age of Muhammad Ali
and his successors, the 1882 British occupation, Alexandria under the occupation, and the era of
national independence. It ends with a list of the author's other works.

### 3. آثار الإسكندرية القديمة (`alex-ancient-monuments`, inside the zip as `f.pdf`)
728 pages by **أ.د. عزت زكي حامد قادوس** (preface signed Alexandria, 15/11/1998). It includes
prefaces to the first and second editions; the second mentions the opening of the new Library of
Alexandria. From the title, it covers the ancient monuments and archaeology of the city.
*I only read the title page and prefaces; the chapter list will be clear once OCR has run, so
add a short description here afterwards.*

## Tuning

Constants at the top of `extract_books.py`: `CHUNK_CHARS` (1000), `CHUNK_OVERLAP` (150), OCR DPI and
`OCR_PSM`, confidence thresholds, and `EMBED_MODEL` (`intfloat/multilingual-e5-base`; use
`multilingual-e5-small` on weak machines). `skip_pages` and `noise_patterns` per book are in
`books_metadata.json`. I skipped the front matter and contents pages I identified (book 1 pages 1–8,
book 2 pages 151–153). Add more ranges if you see junk chunks.

## Status of testing

The cleaning, chunking, page/source tracking, confidence labels and Excel/JSONL/CSV export were run
and verified on sample data. The OCR step and the ChromaDB indexing step were **not** run here: this
environment has no Arabic OCR data and no internet. Run on a small book first and look at a few chunks
in `chunks.jsonl` before indexing everything.
