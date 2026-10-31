# Handoff: semester 1 gaps (31 Oct 2026)

Run from a Claude Code **cloud** session, not on `D:\MyProject`. That had three effects:

- The rule files (`moe-books\CLAUDE.md`, `claude/lesson-versions-pipeline*.md`, `HANDOFF-2026-10-20-browser-ocr-g1-g4.md`) were not reachable, so I matched the format to `validate_plan()` and to existing versions in the DB.
- There was no browser pane, so OCR ran in the container with pymupdf at 300 dpi and tesseract `ara`.
- This file was written in the `book-tracker` repo, branch `claude/new-session-6lp2dm`.

Supabase project: `rznegxkfwwtqkytkoljs`.

## Summary

| Part | Status |
|---|---|
| A: upload two guides to `book_pages` | **Done (second pass, same day).** 164 pages are in `book_pages`: `cls12_TB_World_P1` 122, `cls11_TB_IslamicCivil_P1` 42. All 16 semester 1 lessons have pages. |
| B: 16 lessons × 3 versions | **Done.** 48 versions promoted, 0 failed. |
| C: 13 lessons × 3 versions | **Done.** 39 versions promoted, 0 failed. |
| D: relabels | **Done.** 7 lessons set to semester 2, including the G10 social studies check. |
| A2: real G12 teacher guide (2019) | **Done.** 141 pages as `cls12_TB_World_2019_P1`, `book='teacher'`, semester 1. |
| G12 rewrite from the 2019 guide | **Done.** All 27 versions (9 lessons × 3) rewritten in place. |
| G7 العلوم «أسئلة نهاية الوحدة» | **Done.** v2 and v3 added for all 6 lessons; each has 3 versions. |
| Staging cleanup (5 rows) | **Not done.** The DELETE waits on a confirmation prompt that timed out twice. See below. |
| `tmp_ingest_insert` | **Locked** (`with check (false)`), confirmed in `pg_policies` after each pass. |

## Part A: what happened

### Step 1: confirming the books
Both PDFs downloaded from ict.moe.gov.om:
- `cls12_TB_World.pdf`: 56 MB, 254 pages
- `cls11_TB_IslamicCivil.pdf`: 91 MB, 86 pages

**`cls12_TB_World` is the G12 «العالم من حولي» STUDENT book, not a teacher guide.** Its introduction reads «فيسرنا أن نقدم إلى طلبتنا الأعزاء في الصف الثاني عشر كتاب العالم من حولي». The content is student activities («النشاط», «اقرأ وحلل»). It is the right subject and covers the whole year: units 1–3 are term 1 and match the DB's 9 lessons; units 4–6 are «الفصل الدراسي الثاني».

I kept it, because it is the right subject and term and the only source available. The rows are labelled **`book = 'student'`**, not `teacher`, so the pipeline isn't misled. `source_file` stays `cls12_TB_World_P1` as specified. If you'd rather have it as `teacher`, change it with one `update`.

**`cls11_TB_IslamicCivil` is the real teacher guide** («دليل المعلم لمادة الحضارة الإسلامية», grade 11). It covers the whole year and is organised per unit: contents, objectives, overview, content analysis, then ONE model lesson with activity sheets.
- Unit 1 (pp 20–31) is term 1. Its model lesson is «عوامل ازدهار».
- Unit 2 (pp 32–49) is term 1. Its model lesson is «النظم الإدارية والعسكرية».
- Units 3–4 (pp 50–78) are term 2 and were skipped.

### Steps 2–4: OCR and mapping (done locally)
I skipped the cover, credits, portraits, map, anthem, contents and blank pages. Cleanup dropped junk lines (fewer than 3 Arabic letters, or under 50% alphanumeric), removed bidi marks and collapsed blank lines. Pages with less than 40 characters after cleanup were dropped.

**`cls12_TB_World_P1`: 122 pages, `book='student'`, `semester=1`**

| Pages | Mapped to |
|---|---|
| 14–15, 51–52 | Unit 1 opener and references → all 3 unit 1 lessons |
| 16–27 | إنجازات الأوائل |
| 29–39 | ثورات كبرى |
| 41–49 | عالم الغد |
| 53, 100 | Unit 2 opener and references → all 3 unit 2 lessons |
| 54–65 | الطاقة حياة العالم |
| 66–82 | النفط |
| 83–99 | الطاقة المتجددة |
| 137–139 | Unit 3 references → all 3 unit 3 lessons |
| 102–110 | الثقافة الإنسانية |
| 111–120 | الأقاليم الثقافية |
| 121 | Both الأقاليم الثقافية and الثقافة الوطنية |
| 122–136 | الثقافة الوطنية العمانية |

**`cls11_TB_IslamicCivil_P1`: 42 pages, `book='teacher'`, `semester=1`**

| Pages | Mapped to |
|---|---|
| 6–7, 10–19 | General teaching guidance (methods, skills) → all 7 term 1 lessons, `unit_number` null |
| 20–22 | Unit 1 opener and analysis → all 3 unit 1 lessons |
| 23–31 | عوامل ازدهار (model lesson and its activities) |
| 32–34 | Unit 2 opener and analysis → all 4 unit 2 lessons |
| 35–49 | النظم الإدارية والعسكرية (model lesson and its activities) |

Every one of the 16 semester 1 lessons has at least one page in this mapping.

### First attempt: upload blocked
1. I set a one-time token with migration `tmp_ingest_open_20261031`.
2. The anon REST insert was refused by the container's egress proxy (`rznegxkfwwtqkytkoljs.supabase.co` blocked). No row was inserted.
3. I re-locked straight away with `tmp_ingest_lock_20261031`.

### Second pass: upload done
`supabase.co` was then added to the allowed domains, but the change hadn't reached this already-running container (the proxy still returned 403). New network settings usually only apply to a new session. So the rows went in through the Supabase SQL tool instead:

1. **Open.** I made a fresh one-time token and limited `tmp_ingest_insert` to it with migration `tmp_ingest_open_20261031b`.
2. **Push.** I inserted the same 164 built rows into `tmp_book_ingest` in 18 chunks, each tagged with the token. No re-OCR was needed: the scratchpad rows were still there.
3. **Re-lock.** I set `with check (false)` with migration `tmp_ingest_lock_20261031b` and deleted the token file.
4. **Integrity check.** I compared an md5 for each page against the local OCR output. 163 of 164 matched. Civil p40 had one line with two Arabic diacritics swapped (copy error). I rebuilt that line from exact code points, and after that all 164 matched byte for byte.
5. **Move.** I ran `distinct on (source_file, pdf_page) … order by id desc` → `book_pages` with `semester=1`, `method='ocr'`, the subject_id, and `on conflict (source_file, pdf_page) do nothing`. Result: 122 World pages and 42 Civil pages inserted, with 0 conflicts.
6. **Verify.** All 16 semester 1 lessons have pages:
   - G12 العالم من حولي: 12–19 pages per lesson.
   - G11 الحضارة الإسلامية: 15–30 pages per lesson. The two model-lesson lessons have the most.

   All 16 also have 3 versions.

The staged rows stay in `tmp_book_ingest`, as in earlier runs. `tmp_ingest_insert` was confirmed as `with check (false)` afterwards.

### Staging leftover
(First note, kept for history. See "Staging cleanup" below for the current state.)

The only `plan_import_staging` row besides the four `[pe12-dup]` rows is **id 4723**: G4 الرياضيات «6 المساحة والمحيط (1):6-2 المحيط (1)», staged 29 Oct, which failed validation (Arabic-Indic digits, a forbidden symbol). **It is not a «3.1 التفاعلات» duplicate**: all three 3.1 rows were promoted and then removed by `promote_staged`. I didn't delete it. Delete it or fix its text as you see fit.

## Part A2: the real G12 teacher guide (2019)

You uploaded the real G12 «العالم من حولي» teacher guide (2019, term 1 only) in three parts, split from one 262-page PDF. Part 1 skips original pages 1 and 4 (cover and portrait).

- **OCR.** The PDF text layer is garbled, so every page was OCRed (pymupdf 300 dpi, tesseract `ara`), with the same cleanup as before.
- **Numbering.** `pdf_page` is the **original** PDF page number. Printed page = PDF page − 1.
- **Skipped.** Pages 2, 3, 5, 6 and 7 (title, credits, contents).
- **Upload.** 141 rows went through a fresh one-time token on `tmp_ingest_insert` (migrations `tmp_ingest_open_20261031c` / `tmp_ingest_lock_20261031c`) by anon REST, since `supabase.co` was reachable by then.
  - The aggregate md5 `d8e4b67c5972f0fd75e20d8bb4ae4334` matched locally and in the DB.
  - Rows were moved to `book_pages` with the same `distinct on … on conflict do nothing` pattern: 141 inserted.
  - The policy was re-locked to `with check (false)` and the token file deleted.
- **Placement.** The rows sit next to the existing student-book rows (`cls12_TB_World_P1`).

**`cls12_TB_World_2019_P1`: 141 pages, `book='teacher'`, `semester=1`**

| PDF pages | Mapped to | `unit_number` |
|---|---|---|
| 8–26 | General teaching guidance → all 9 lessons | null |
| 27, 60, 61 | Unit 1 opener and closing pages → all 3 unit 1 lessons | 1 |
| 28–39 / 40–50 / 51–59 | إنجازات الأوائل / ثورات كبرى / عالم الغد | 1 |
| 62, 109 | Unit 2 opener and closing → all 3 unit 2 lessons | 2 |
| 63–75 / 76–91 / 92–108 | الطاقة حياة العالم / النفط / الطاقة المتجددة | 2 |
| 110, 146–148 | Unit 3 opener and closing → all 3 unit 3 lessons | 3 |
| 111–119 / 120–130 / 131–145 | الثقافة الإنسانية / الأقاليم الثقافية / الثقافة الوطنية العمانية | 3 |

Each of the 9 lessons now has 31–38 guide pages, plus its 12–19 student-book pages.

### G12 rewrite from the guide

All 27 versions were rewritten from the 2019 guide.
- **Content.** Each lesson follows the guide's objectives in order, its number of periods, its prior-learning opener and its per-objective activities. Each also includes the guide's enrichment and remedial activities.
- **Strategies.** v1 follows the guide's own strategy (discussion, debate and role-play; essential questions for 3.1; the systemic approach for 3.3). v2 is cooperative learning and v3 is problem solving or inquiry.
- **Method.** Rows were staged and validated, then written into the existing `lesson_plan_versions` rows with an UPDATE. That keeps the ids and `source`. `promote_staged(12)` then cleared the staging rows: inserted 0, failed 0, `missing_lesson = 4` (the old `[pe12-dup]` rows).
- **Verified.** 3 versions per lesson, 300–417 words. Every version states its period count, and they match the guide:

| Lesson | Periods |
|---|---|
| 1.1, 1.2, 1.3 | 3 each |
| 2.1, 2.2, 2.3 | 4 each |
| 3.1 | 4 |
| 3.2 | 3 |
| 3.3 | 4 |

- **OCR quirk.** In this guide's OCR the digit 3 often comes out as `"` or `”`. The period counts were checked against the page images.

The Part B notes below describe the first G12 versions (written from the student book). Those have now been replaced.

## G7 العلوم «أسئلة نهاية الوحدة»: v2 and v3 (12 versions)

Written the same way as G8:
- **v2:** cooperative learning or stations.
- **v3:** problem solving.

Each is built on that unit's actual end-of-unit questions in `cls7_Student_P1_science`:

| Lesson | Pages | Topic |
|---|---|---|
| 1-11 | 36 | Flower parts, fruit, seed dispersal |
| 2-8 | 52 | Heating graph, best-fit line, anomalous reading, constant temperature |
| 3-15 | 81–82 | Energy stores and transfers, fuels, solar cell, conduction/convection/radiation, evaporation |
| 4-9 | 99–100 | Cell parts, yoghurt and pH, yeast dough investigation |
| 5-10 | 120 | Rock fragment deposition, dual-axis graph |
| 6-7 | 135–136 | Sunrise, Earth's rotation, solar system, reflected light, stars in January vs July |

`promote_staged(7)`: submitted 12, inserted 12, failed 0, missing 0. All 6 lessons were verified at versions 1, 2 and 3.

## Staging cleanup

The 5 leftover rows are still in `plan_import_staging`:
- 4723: the G4 maths row that fails validation.
- 5836, 5887, 5888, 5889: «خطط اللعب في كرة القدم [pe12-dup]».

The `delete … where id in (4723,5836,5887,5888,5889)` triggers a confirmation prompt from the Supabase server, and it timed out unanswered twice. It needs to be approved, or run by hand:

```sql
delete from plan_import_staging where id in (4723,5836,5887,5888,5889);
```

## Part B: versions added (48)

Written from the sources above, following their order, activities and objectives. Humanities format: 6 fixed headers, no formulas, no page references, Western digits. v1 uses الحوار والمناقشة, v2 التعلم التعاوني, v3 حل المشكلات or الاستقصاء.

- **G12 العالم من حولي:** 9 lessons × 3 = 27. Inserted 27, failed 0. `missing_lesson = 4`: these are four older grade 12 staging rows titled «خطط اللعب في كرة القدم [pe12-dup]» that predate this job. I left them alone.
- **G11 الحضارة الإسلامية:** 7 lessons × 3 = 21. Inserted 21, failed 0, missing 0.
  - «عوامل ازدهار» and «النظم الإدارية والعسكرية» follow the guide's model lessons step by step.
  - The other 5 lessons follow the guide's unit-level objectives, concepts and generalisations. The guide has no per-lesson plan for them, and the G11 student book isn't in `book_pages`.

Word counts are 300–392. Most v2/v3 are 300–360, under the ~380 target but all pass `validate_plan`. Six drafts came in at 278–297 and were extended to 300 or more before promoting.

## Part C: versions added (39)

| Subject | Versions | Sources |
|---|---|---|
| G11 العلوم البيئية: 3.1, 3.2, 3.3, 4.1, 4.2 | 15 | Student book `cls11_Student_P1_env` pp 84–152 and activity book `cls11_Activity_P1_env` pp 101–161 (both already in `book_pages`). No teacher guide pages exist for units 3–4. |
| G8 العلوم «أسئلة نهاية الوحدة», units 1–6 | 18 | Planned as unit review periods built on each unit's end-of-unit questions (student book pp 32–33, 54–55, 68–69, 80–81, 98–99, 116–117). |
| G9 احياء 6-3 العين, 6-4 الهرمونات | 6 | Same content as the older duplicates «3-6 العين» and «4-6 الهرمونات». Those duplicates already have 3 versions each and were left alone. |

`promote_staged` results: grade 11 inserted 15/15, grade 8 18/18, grade 9 6/6. All had `failed = 0` and `missing_lesson = 0`. Each of the 13 lessons was verified at exactly 3 versions.

## Part D: relabels (7)

Each one is logged in `semester2_relabel_20261026` with a `why` starting «2026-10-31».

- G10 اللغة الانجليزية «Unit4: Entrepreneurship: Lesson6»
- G5 الرياضيات «19 الطول: 19-1 قياس ورسم الخطوط»
- G5 الرياضيات «22 العدد: الاستراتيجيات الذهنية : 22-1 استخدام الاستراتيجيات الذهنية»
- G7 اللغة الانجليزية «Learning Club1: Lesson1», «…Lesson2», «…Lesson3». These are duplicates; they were relabelled, not deleted.
- G10 الدراسات الاجتماعية «الحياة الثقافية والاجتماعية والاقتصادية». This is lesson 3 of the unit «عمان خلال القرون الثلاثة الأولى من الهجرة». The term 1 guide `cls10_TB_Social_P1` only reaches that unit's opener (pp 105–113), and the unit's two other lessons were already semester 2 (26 Oct). So it is **semester 2**, and no versions were written.

## Gap check (end of run)

Every lesson with semester 1 or NULL has 3 or more versions. The 6 G7 العلوم «أسئلة نهاية الوحدة» lessons were the last gap. They had 1 version each and now have 3 (see above).

## Skipped and why

- **Part A upload:** first attempt blocked, finished in the second pass (see above).
- **G2 العلوم:** not part of the job. Its term 1 guide isn't on the ICT site.
- **Term 2 content** of both PDFs (World units 4–6, Civil units 3–4): not mapped.
