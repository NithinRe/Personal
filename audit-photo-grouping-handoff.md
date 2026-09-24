# Audit Photo Grouping: Full Context Handoff

Written to bring a new session up to speed on everything done so far. Read top to bottom.

---

## 1. Where this came from

My manager suggested using photo metadata to segment site audit photos into groups. I thought the idea was naive, because it seemed to depend on auditors following a capture process on site, and if they did not follow it, that would be a direct failure.

There was an existing PRD and algorithm design document (v0.1) proposing a five stage pipeline: Ingest, Group, Classify, Extract, Reconcile. Photos come back from a site visit in one undifferentiated folder. Someone manually works out which photos belong to which unit, then transcribes nameplate details (make, model, serial) into a spreadsheet by hand.

Constraints that were settled during the work:

* There is no capture app and none is planned. The algorithm is the backbone of the product.
* A "unit" means one machine, not a room or a space. This closed the biggest ambiguity in the original document.
* v1 is grouping only. Given a dump of photos, sort them into groups regardless of what the subject is. Classification and nameplate extraction are deferred.

---

## 2. The argument that shaped the approach

My original objection did not hold. The time gaps between photos do not come from process discipline, they come from physics: a person cannot photograph two machines at once and has to walk between them. Walking takes minutes, shooting takes seconds. That difference appears whether or not anyone follows a procedure.

The real objection, which I arrived at later, is different and better: if the process is enforced anyway, a single "new unit" button press at capture time would give 100 percent grouping accuracy for free, and all of the threshold machinery exists to reconstruct information that was thrown away at capture. Since there is no app, the algorithm is the correct fallback. That question is now closed.

A second objection I raised turned out to be the strongest finding in the whole exercise: **the revisit case**. If an auditor returns to a machine later in the same visit, no timestamp rule can connect those two sessions. The only thing that can link them is the serial number, which comes from Stage 4 of the pipeline, while grouping is Stage 2. The original pipeline runs strictly forward and never loops back. Conclusion: grouping must be provisional and revisable after extraction, not locked before it. This is still unsolved and is an architecture issue, not a tuning issue.

---

## 3. The algorithm, in plain terms

The method reads only the capture timestamp from each photo. It never opens the image. Machines, trees, taps, pineapples: all identical to it.

Vocabulary used throughout (deliberately plain, because the report audience is non technical):

* **Pause**: seconds between one photo and the next. 42 photos gives 41 pauses.
* **Typical pause (median)**: the middle value of all pauses. The auditor's normal rhythm that day.
* **Variation (MAD, median absolute deviation)**: how far a pause typically sits from the median. Measured by taking each pause's distance from the median, then taking the median of those distances.
* **k**: an adjustable dial. How many units of variation above normal counts as unusual.
* **Cut off**: `median + k * MAD`. A pause longer than this is treated as a walk to a new unit.

MAD is used instead of standard deviation because the large gaps are exactly what we are hunting, and they would inflate a standard deviation and hide themselves. The median does not get dragged by outliers.

**Final proposed algorithm:**

1. Split the dump into visits. Any gap over 2 hours means a different day or site. Handle each visit completely separately, because two sites will not share a working pace.
2. Split each visit by camera (EXIF Make, Model, BodySerialNumber). Different phones have different clocks.
3. Collapse bursts. Photos under 2 seconds apart count as one moment when measuring pace, otherwise they drag the median toward zero and flatten MAD.
4. Measure median and MAD.
5. Cut off = median + 3 x MAD. Never below 1.5 x median. Never above 10 minutes.
6. Any pause over the cut off starts a new group.

---

## 4. Test 1: apartment (indoor, tight spacing)

20 photos, iPhone 15 Pro, 7 subjects, about 3 minutes. Subjects: bathroom faucet (5 photos), toilet (3), thermostat (1), Instant Pot (4), microwave (1), water heater closet (5), dishwasher (1). All EXIF intact, one device, no bursts.

| Metric | Value |
|---|---|
| Median gap | 4.9 s |
| MAD | 1.6 s |
| MAD / median | 0.32 |
| Cut off at k=3 | 9.7 s |
| Separation ratio | 1.86x |
| Best accuracy | 85 percent at a 10 s threshold |
| Result | 5 of 6 boundaries found, 0 over splits, 1 miss |

**Three findings from this run:**

1. **The 120 second floor in the original PRD would have destroyed it.** The formula gave a correct answer of 9.7 s. The floor clamped it to 120 s. No gap in the dataset exceeded 120 s. Result: zero cuts, all 20 photos in one group, 25 percent accuracy, and no indication anything went wrong. The floor is not a safety net, it is a kill switch when equipment is close together. It must be expressed relative to measured pace, not in fixed seconds.

2. **The one failure was the costly kind.** The faucet to toilet transition had a gap of 4.9 s, identical to the median within unit gap. I turned around, zero walk. That is an under split: two different fixtures merged into one group. Downstream, Stage 5 would fuzzy cluster values from two different fixtures and confidently pick a winner. Under splits corrupt the asset register invisibly. Over splits are cheap by comparison (a reviewer hits merge).

3. **GPS is dead indoors.** Mean horizontal error 6.0 m, peak 12.1 m. The 7 units spanned 8.3 m by 6.3 m total, with centroids 0.8 m to 7.1 m apart. The noise exceeds the signal. The PRD's rule treating GPS distance as a hard boundary must be deleted.

Side note: image resolution jumped from 4032x3024 to 5712x4284 at photo five, meaning a lens or mode change mid walk. That is an unexploited free signal, since a capture setting change often coincides with moving to a new subject.

---

## 5. Test 2: trees (outdoor, wide spacing)

42 photos, same phone, 11 trees, 7 minutes 45 seconds. All timestamps present, one camera. Tree sizes: 4, 4, 3, 4, 5, 4, 4, 4, 3, 3, 4 photos.

| Metric | Value |
|---|---|
| Median gap | 8.3 s |
| MAD | 1.9 s |
| MAD / median | 0.23 (lower than indoors) |
| Cut off at k=1.9 | 12 s |
| Groups found | 9 (against 11 real trees) |
| Separation ratio | 2.4x |
| Accuracy | 37 of 41 boundary decisions correct (90 percent) |

**The four errors at a 12 s cut off:**

| Gap | What happened | Type |
|---|---|---|
| 8.3 s | Trees 5 to 6, real walk, called same tree | missed |
| 9.5 s | Trees 7 to 8, real walk, called same tree | missed |
| 11.0 s | Trees 8 to 9, real walk, called same tree | missed |
| 12.6 s | Inside tree 9, same tree, called a walk | false cut |

Because 7 to 8 and 8 to 9 both failed, trees 7, 8 and 9 merged into one blob of eleven photos, which was then chopped in the middle. Trees 1 to 4 and 10 to 11 came out perfectly. All damage was in the middle stretch.

**The critical observation:** three real tree to tree gaps (8.3 s, 9.5 s, 11.0 s) were shorter than the longest within tree gap (12.6 s). The two populations overlap. No cut off can separate them.

**Why my spacing hypothesis was wrong.** I predicted outdoor would separate cleanly at 4x or better because trees are far apart. It came back at 2.4x. Walk distances versus times:

* Tree 5 to 6: 15 metres in 8.3 seconds
* Tree 8 to 9: 4 metres in 11.0 seconds
* Tree 6 to 7: 56 metres in 66.0 seconds

A brisk 15 metre walk took less time than one careful photo. The MAD to median ratio actually went *down* outdoors (0.23 versus 0.32), meaning my outdoor pace was more uniform, which is the condition where the algorithm has the least to work with.

**GPS outdoors worked.** Trees spread over 52 m by 90 m, consecutive trees 4 m to 56 m apart. Signal much larger than noise. So the defensible claim is: GPS is usable outdoors, unusable indoors, and can only ever suggest a merge, never force one.

**Conclusion across both runs:** accuracy is a function of how long a walk takes relative to how long a photo takes, not of how disciplined the auditor is and not of how far apart the equipment is.

---

## 6. Test 3: does adding AI help?

Ran two vision models over the same 42 tree photos via Claude Code, asking one narrow question 41 times: do these two consecutive photos show the same tree or a different one? Models were not told the answers. Two cheap non AI image comparisons were computed as controls.

| Method | Correct out of 41 |
|---|---|
| **Capture times alone** | **37** |
| Colour histogram | 34 |
| Haiku 4.5 | 33 |
| pHash | 31 |
| Sonnet 5 | 31 |

**Both models performed worse than arithmetic.** One performed worse than a plain colour comparison requiring no model at all. This is worth keeping in the report precisely because "just add AI" is the first thing most people suggest.

The dominant error was not confusing one tree with another. Sonnet made 6 false cuts: it looked at two photos of the *same* tree and said "different." That is a viewpoint problem, since a wide shot and a bark close up look nothing alike. That does not get easier with equipment, since a wide shot of a chiller and a close up of its nameplate are at least as dissimilar.

**Where the model does earn its place.** Every error sat close to the cut off. Gaps far from the line were always right. So flag gaps near the line as doubtful and send only those to the model.

| Configuration | Correct out of 41 |
|---|---|
| Clock alone | 37 |
| Clock + one model on doubtful gaps only | 38 |
| **Clock + two models required to agree, doubtful gaps only** | **39** |
| Model consulted on every gap | 31 to 33 |

**Band width sweep** (which gaps get sent to the models):

| Band | Pairs sent | Haiku | Sonnet | Both must agree |
|---|---|---|---|---|
| 11 to 13 s | 2 | 38 | 39 | 38 |
| 10 to 14 s | 6 | 37 | 38 | 38 |
| 9 to 14 s | 10 | 37 | 38 | 38 |
| 8 to 13 s | 15 | 38 | 38 | **39** |
| 8 to 15 s | 15 | 38 | 38 | **39** |
| 7 to 16 s | 23 | 38 | 36 | **39** |
| 6 to 20 s | 33 | 37 | 35 | 38 |
| everything | 41 | 33 | 31 | 36 |

The pattern held everywhere: the narrower the band, the better the result. Handing the models everything was worse than handing them nothing.

**The agreement rule is the robust choice.** Sonnet alone produced false cuts at almost every band width (1, 2, 4, 6). The two model agreement rule produced **zero false cuts at every band width**, because two models rarely make the same mistake at the same moment. They disagreed on 10 pairs, with Haiku right on 6 and Sonnet right on 4, so neither dominates and requiring agreement is a genuine safeguard rather than a coin flip.

Commercial angle: the expensive step runs on roughly a third of the decisions instead of all of them, and produces a better answer. Lower cost and higher accuracy arrive together.

One pair nobody ever got right: trees 7 to 8, a 9.5 second gap. Clock missed it, both models said same tree, colour histogram will not save it. That one needs a human.

---

## 7. Proposed architecture

```
Folder of photos
   |
Separate the streams  (one visit, one camera, bursts counted once)
   |
Measure the pace  (median gap and MAD)
   |
Set the cut off  (median + 3 x MAD, with floor and ceiling)
   |
   +--> pause clearly short or clearly long  -->  clock decides, free
   |
   +--> pause close to the cut off  -->  compare the two photos
                                          -->  two checks must agree
                                          -->  otherwise keep the clock's answer
   |
Photos grouped by unit  (plus a small human review queue)
```

Two properties worth naming: the system knows which of its own answers are unreliable, and it knows this for free, simply from how close each pause sat to the cut off. And every automatic decision stays overridable.

---

## 8. Prior art

This is not novel and the report does not claim it is. It is called **temporal event clustering** in the consumer photography literature, dating from the early 2000s, with Kodak patents on variants. Loui et al. segmented collections into events using timestamps, then broke each event into sub events using colour histogram similarity. Time first, then image content to refine. That is exactly the architecture above, published twenty years ago. Framing it as established prior art is stronger than claiming invention.

Alternatives worth testing in future:

* **DINOv2 embeddings** instead of asking a VLM. Trained with contrastive learning to match objects across viewpoints, which is precisely the failure Sonnet showed. Deterministic, free after download, runs locally, gives a distance number you can threshold instead of an uncalibrated yes or no. This is the most promising untested option.
* **Similarity matrix with a novelty score** instead of a single threshold. Multi scale, so it would handle a site where some equipment is 30 seconds apart and other equipment is 8 minutes apart, which one global cut off cannot.

---

## 9. Edge cases identified but not yet tested

Tier 1, silent killers:

* **EXIF stripped in transit.** WhatsApp removes all EXIF. Email clients re encode. One habit and the entire signal is gone, with no crash. Never fall back to file modification time, since copying resets it. Detect stripped batches at ingest and refuse loudly.
* **Two cameras** with clocks offset by minutes scrambles every boundary while looking plausible. Partition by device before sorting anything.
* **Burst mode** collapses the gap statistics toward zero.
* **Duplicate votes in reconciliation.** Near identical frames misread the same way outvote one good read from a different angle. Consensus voting only works if votes are independent. Dedupe by perceptual hash before voting.
* **Fabricated serials.** Make it structurally impossible: every returned field must carry a bounding box in the source image and a character level OCR confidence. Use a generative model only to choose among OCR tokens, never to produce a value.
* **One character serial errors** (O/0, I/1, S/5, B/8, 2/Z). A serial with one wrong character is still a valid looking serial and nothing downstream can catch it.

Tier 2, the algorithm eating itself:

* **Uniform pace**: MAD near zero means the threshold sits at the median and roughly half of all gaps "exceed" it. Correct output in that case is "no detectable structure, group manually." Refusing is a feature.
* **Too few photos**: below about 10 gaps the median and MAD are unstable.
* **Multiple spatial scales in one site**: argues for hierarchical clustering over a flat threshold.
* **Interruptions** (lunch, phone call) produce false splits mid unit.
* **Timezone and DST**: DateTimeOriginal carries no timezone. Exact 3600 second jumps are suspicious.

Tier 3, capture reality:

* Context photos (door signage, panel schedules, site plans) land *between* units, exactly where the boundary is. But door signage often names the unit, so treat as a distinct non asset class.
* One photo showing two units. Genuinely ambiguous membership.
* Multiple plates in one frame (main unit, compressor, motor).
* Client asset tags versus OEM nameplates.
* Junk photos (pocket shots, floor, finger over lens).

---

## 10. Deliverables produced

1. **gap-check.html**: a self contained browser tool. Drop a folder of photos in, it reads EXIF locally (nothing uploaded), computes median, MAD, cut off, shows a gap spectrum and timeline, lets you tick ground truth per photo, scores itself, and exports CSV. Contains no AI. Default floor is 120 s and should be set to 1 before reading anything, otherwise it reproduces the failure described in section 4.
2. **Audit-Photo-Grouping-Report.docx**: 12 section Word report, black Times New Roman, three figures. Percentages kept out of the prose, every number in a table with its source stated. Delivered to my manager on 2026 08 05. Decision on whether to build a tool now rests with them.

---

## 11. Where things stand and what is next

Settled: unit means one machine. v1 is grouping only. No capture app. The algorithm is sound and delivers roughly 90 percent of boundary decisions from arithmetic alone.

Still open:

* **The revisit case.** No timestamp rule can re link a machine photographed twice in one visit. Needs the serial, which means grouping must be revisable after extraction.
* **Part Two**: repeat on genuine audit photographs from two or three real sites, captured by different auditors, ground truth recorded on site. Both existing datasets came from one person on one phone, and the cut off is derived entirely from pace. Specific questions: does the doubtful band land in the same range for a slower auditor; do capture times survive the transfer route auditors actually use; how often does a genuine revisit occur; how are context photos handled; does visual comparison behave differently on equipment.
* **Part Three**: nameplate extraction. Deliberately not started. The difficulty is not reading the text, it is that an incorrect reading is indistinguishable from a correct one.
* **User research**: observe an auditor on a real visit rather than interviewing them, since people cannot report their own rhythm accurately. Also time the current manual reconciliation end to end, because there is no baseline and without one the "60 percent time reduction" target is uncheckable. The reviewer matters as much as the auditor, since they are the person currently doing the sorting by hand.

Four questions from the original document that still have no answer: do auditors work unit by unit or double back; one photo per nameplate or several; which asset types actually matter to the client; is on device processing a hard requirement or is a server side OCR service acceptable.
