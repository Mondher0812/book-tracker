# Handoff: the last 7 subjects with no lessons (31 Oct 2026)

Written in a Claude Code **cloud** session, in the `book-tracker` repo on branch `claude/new-session-6lp2dm`.

- Supabase project: `rznegxkfwwtqkytkoljs`.
- OCR ran in the container with pymupdf at 300 dpi and tesseract `ara`.
- No Anthropic or OpenAI API calls. I wrote every lesson version in the session.
- Network: `ict.moe.gov.om` and `rznegxkfwwtqkytkoljs.supabase.co` were both reachable.

**Status: part done.** Grade 10 music is finished. Grades 11 and 12 have their lessons but no versions yet. Mondher is sending the G11 teacher guide. Life skills G5–G8 are not taught in 2026/27, so I created no lessons for them.

## Summary per subject/grade

| Grade | Subject | Taught in 2026/27? (flyer page) | Lessons created | Versions | Books used / uploaded |
|---|---|---|---|---|---|
| 5 | المهارات الحياتية | **No.** The flyer covers only «الصفوف من (9-10)» (`flyer_Maharat_2026` p.2, p.6). | 0 | 0 | None. The existing `cls5_*` pages are untouched. |
| 6 | المهارات الحياتية | **No.** Same as G5 (p.2, p.6). | 0 | 0 | None. The existing `cls6_*` pages are untouched. |
| 7 | المهارات الحياتية | **No.** p.5: «تم حذف منهج المهارات الحياتية للصفين السابع والثامن من الخطة الدراسية الجديدة». | 0 | 0 | None. The existing `cls7_*` pages are untouched. |
| 8 | المهارات الحياتية | **No.** Same as G7 (p.5). | 0 | 0 | None. The existing `cls8_*` pages are untouched. |
| 10 | المهارات الموسيقية | **Yes.** It sits in the «الصفوف (5-10)» section; plan on `flyer_Music_2026` p.47–48, 1 period a week, 15 weeks. | 9 | **27 (3 each)** | Teacher guide (sent by Mondher) uploaded as `cls10_TB_Music_P1`, pdf p.7–41, 35 pages. |
| 11 | المهارات الموسيقية | **Yes.** Plan on p.53–55, 5 periods a week. | 11 | **0, pending** | None yet. The guide is coming from Mondher. |
| 12 | المهارات الموسيقية | **Yes.** Plan on p.57–58, 5 periods a week. | 12 | **0, pending** | None found. ICT has no guide and no student book. |

### Searching for a merger into another subject (G5–G8 life skills)
I searched every OCR'd flyer for «الحياتية», «حذف» and «دمج». The flyers searched were العلوم, العربية, الرياضيات, الإسلامية, المواطنة ×2, الفنون, الرياضة and الموسيقى. None of them takes in life skills content for G5–G8. The only hits were unrelated phrases, «مسيرتهم الحياتية» and «تطبيقات حياتية».

## Lesson titles created

All titles are term 1 (`semester = 1`) and come from the flyer's term 1 distribution plan. They follow the style of the reference subjects: one lesson per topic, as in G7/G8 music, and no numbering prefix. Before inserting, I checked each title against existing lessons in its subject, ignoring spaces and diacritics. All 7 subjects had 0 lessons, so there were no clashes.

**G10** (`bd709b04-…`). All 9 titles match the teacher guide's table of contents exactly.
- Unit 1 «قواعد ونظريات»
  1. مفتاح (فا) الخط الرابع
  2. سلم (فا) الكبير وسلم (ري) الصغير الهارموني
  3. التآلف الكبير والتآلف الصغير
- Unit 2 «فن المولد»
  1. فن المولد
  2. نشيد (يا نبي سلام عليك)
  3. آلات من التراث العماني
- Unit 3 «تاريخ وتذوق»
  1. العصر الرومنسي (الرومانتيكي) (1800-1900م)
  2. جوزبيه فيردي (1813-1901)
  3. عزف مقطوعة موسيقية

**G11** (`46b2352c-…`). The flyer notes on p.55 and p.4 move the whole of unit 4 to term 2 and bring lessons 1–2 of unit 5 into term 1. The lessons below follow that.
- Unit 1
  1. عنصر الإيقاع
  2. عنصر النغم
  3. عنصر التعبير (التظليل)
- Unit 2
  1. التآلفات الهارمونية
  2. المصاحبة الهارمونية
  3. عزف الألحان الهارمونية
- Unit 3
  1. نبذة تاريخية لآلة الجيتار
  2. مكونات آلة الجيتار
  3. طريقة العزف على آلة الجيتار
- Unit 5
  1. فن الصوت
  2. من فن الصوت (صوت عمان الجميلة)

**G12** (`b616e89b-…`)
- Unit 1
  1. الطبقات الصوتية
  2. التنفس والرنين
  3. تدريبات عملية لتربية الصوت
- Unit 2
  1. قواعد هارمونية
  2. مصاحبة هارمونية
  3. عزف تآلفات هارمونية
- Unit 3
  1. انتشار الموسيقا في الأندلس
  2. زرياب (777م-852م)
  3. الموشحات الأندلسية
- Unit 4
  1. تصوير السلالم والألحان
  2. الأشكال الإيقاعية غير المألوفة
  3. صولفيج غنائي

The G11 and G12 plans have «تطبيقات» periods after many topics. I did **not** make these separate lessons. One reason is that the identical titles would collide in `promote_staged`, which matches lessons by title. The other is that the reference subjects don't do it either. Their periods are counted inside the preceding lesson.

## Books

- **ICT TeacherBook:** in `XMLjs/class{10,11,12}.js`, the music entries list **no file** for any of the three grades. The entries are «كراسة المهارات الموسيقية» for G10 and G12, and «كراسة نشاط مهارات موسيقية» for G11.
- **ICT student books:** G10 has «Karas Nashat Al Maharat Al Musiqi Class 10_Classical» (56 pages). G11 has «karas nshat music_Classical» (104 pages). G12 has no music entry.
  - I downloaded both booklets but only started OCR. **Neither is uploaded to `book_pages`.**
- **G10 teacher guide:** دليل المعلم لمادة المهارات الموسيقية للصف العاشر, first edition 2012, 75 pages. Mondher uploaded it to the session; the cover and contents confirm the subject and grade.
  - Printed page numbers equal PDF page numbers.
  - Uploaded pages 7–41 (introduction, objectives, period plan, units 1–3) as `source_file = 'cls10_TB_Music_P1'`, `book = 'teacher'`, `semester = 1`, `method = 'ocr'`.
  - Linking:
    - unit openers p.13 (U1), p.25–26 (U2) and p.35 (U3) go to every lesson in their unit
    - U1: p.14–17 → L1, p.18–21 → L2, p.22–24 → L3
    - U2: p.27–28 → L1, p.29–31 → L2, p.32–34 → L3
    - U3: p.36–37 → L1, p.38–39 → L2, p.40–41 → L3
    - front matter p.7–12 stays unlinked
  - Pages 42–75 (units 4–6, term 2) were not uploaded.
  - Upload path: open a one-time token on `tmp_ingest_insert`, POST through REST with the anon key, re-lock with `with check (false)`, then move with `distinct on (source_file, pdf_page) … order by id desc` and `on conflict do nothing`.

## Versions (G10)

- Each of the 27 versions has exactly the 6 headers, uses Western digits, and has no page references, markdown or bullets.
- Strategy rotation:
  - v1: التعلم بالممارسة والأداء
  - v2: التعلم التعاوني
  - v3: التعلم بالاكتشاف
- Content follows the guide's objectives, materials and suggested steps, in its order. Period counts come from the flyer.
- **Length:** 300–386 words each, a little under the ~380 target for v2 and v3. All pass `validate_plan`, which requires at least 300.
- **Delivery to staging:** to avoid pasting about 35 KB through SQL, I posted the drafts as JSON rows (`book = 'plan'`, `source_file = 'plans_g10'`) through the same token-gated `tmp_book_ingest`. Then I re-locked it, inserted the rows into `plan_import_staging` with `errors = validate_plan(content)`, and deleted the temporary rows.
- `promote_staged(10)` returned: submitted 27, inserted 27, **failed 0, missing_lesson 0**.

## Needs Mondher

1. **Delete life skills G5–G8?** The 2026/27 flyer doesn't teach them; see p.5 and p.2/p.6 of `flyer_Maharat_2026`. I left the 4 subjects (ids in the table above) and their `book_pages` (`cls5_Student_P1_lifeskills`, `cls5_TB_Maharat`, `cls6/7/8_Student_P1_lifeskills`, `cls6/7/8_TB_Skills`) untouched.
2. **G11 guide:** waiting for it. Once it arrives, the remaining work is: check the 11 titles against it, upload and link the term 1 pages, then write 33 versions.
3. **G12:** no guide or student book on ICT. Unless you have one, the 36 versions will be written from the flyer alone.
4. **Titles to sanity-check against a Noor tree later:**
   - G10 «سلم (فا) الكبير وسلم (ري) الصغير الهارموني» and «جوزبيه فيردي (1813-1901)». I used the guide's spelling «جوزبيه», not «جوزيبي».
   - G12 «زرياب (777م-852م)».
5. **Student activity booklets for G10 and G11:** decide whether you want them uploaded to `book_pages` as `book = 'student'`. They are downloaded but not OCR'd in full.

## Step 5 checks (as of writing)

1. **Every new lesson has exactly 3 versions:** G10 passes, 9/9 lessons with 3 each. G11 (11 lessons) and G12 (12 lessons) have 0 versions so far; they are pending.
2. **Life skills G5–G8 lessons have linked pages:** not applicable, because no lessons were created.
3. **`tmp_ingest_insert` is locked:** yes. `with check (false)` is confirmed in `pg_policies` after each of the two token openings.
4. **`plan_import_staging` is empty:** yes, 0 rows. No temporary `book = 'plan'` rows are left in `tmp_book_ingest`.
5. **Gap check** (every lesson with semester 1 or NULL has at least 3 versions):
   - Below 3: only **G11 المهارات الموسيقية (11 lessons)** and **G12 المهارات الموسيقية (12 lessons)**, both new and pending as above.
   - Subjects with 0 lessons: **المهارات الحياتية G5, G6, G7, G8**. They are not taught in 2026/27 and are waiting for Mondher's decision on deletion.
