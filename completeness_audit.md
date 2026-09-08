# LSM Module Test Report — completeness audit (2026-07-08)

Systematic pass over `main.tex`: checked every `\ref`/`\label` pair, every `\cite` key against a bibliography, every `\includegraphics` against the actual `figures/` directory, and read every section for placeholder/incomplete content. No `references.bib` exists in this folder at all, so every citation in the report is currently unresolved.

## Category A — Broken references, fixable now with no new input
These would render as "??" or "[?]" in a compiled PDF.

1. `\ref{sec:XXX}`–`\ref{sec:YYY}` (Summary, line 134) — literal placeholder labels, never defined. Needs pointing at real section labels once (2) below is done.
2. **No section-level `\label{}` on any `\section{}` command in the whole document** — Summary, Set Up, Procedure for data acquisition, High-temperature image analyses, Low-temperature image analysis, Appendix all lack labels. This is *why* (1) is a placeholder — there was nothing to point it at. Fix: add labels to all six, then fix (1) to reference the real range.
3. `\ref{sec:column_defects}` (line 539) — used but never defined. Fix: add `\label{sec:column_defects}` to "Column defects in CCD active region" (line 478) — the intended target is unambiguous from context.
4. `\ref{sec:modulepack}` (line 618, "iron foils attached to the storage box lids, see Sec.~\ref{sec:modulepack}") — never defined, and module packaging isn't actually covered in this report at all — that's Traina et al.'s scope. This should probably become `\cite{Traina2025}` instead of an internal `\ref`. **Judgment call, flagging rather than just fixing.**
5. `\ref{fig:JHUChamberExterior}` (line 225), `\ref{fig:MODULE_INSTALLATION}` (line 412), `\ref{fig:ironfoil}` (lines 625, 633), `\ref{fig:front_composite_example}` (line 701), `\ref{fig:back_composite_example}` (line 756) — all referenced, **none defined, and no corresponding image file exists in `figures/` either.** These aren't broken labels, they're missing figures entirely (see Category D).

## Category B — Pure arithmetic, data already in the document
Seven `\dots` placeholders in "Acquisition sequence" (Sec. 3.1, lines 442–461): per-image readout time for Image_4_135, Image_5, Image_6, Image_7, Image_3_135, Image_4_185, Image_3_185, Image_2, Image_1, plus the two total-acquisition-time sums (135 K and 185 K). All computable directly from the sequencer timing table already in the document (`tab:sequencer_timing_dm13`: clock period = 10 ns, transfer delays, NDCM, image dimensions from `tab:image_summary`) — no new measurement needed, just the arithmetic, same category as the sensor-paper's processing-time row.

## Category C — Wrong content (not missing, actually incorrect)
Three figures have the **wrong caption**, copy-pasted verbatim from the serial-register-defects figure:
- `fig:Single_e_res_all` (line 594) — captioned as "Number of SR defects... module DM18," should describe single-electron resolution across all modules.
- `fig:Gain_all` (line 602) — same wrong caption, should describe amplifier gain across all modules.
- `fig:DC_all` (line 610) — same wrong caption, should describe dark current across all modules.

**Also flagging**: `figures/` contains three files never referenced anywhere in `main.tex` — `Low_temp_SER_modules.png`, `Low_temp_gain_modules.png`, `Low_temp_DC_modules.png`. Names strongly suggest these are the intended (possibly newer/cleaner) versions of the three mis-captioned figures above, which currently point to `image31_low_res_all_modules.pdf`, `image31_low_gain_all_modules.pdf`, `image31_low_dark_current_all_modules.pdf`. **Need your call**: use the `Low_temp_*` files instead, or keep the current ones and just fix the captions?

## Category D — Missing figures (no file exists, need real content)
- Chamber exterior photo (`fig:JHUChamberExterior`)
- Module batch installation photo (`fig:MODULE_INSTALLATION`)
- Iron-foil σ_xy-vs-x scatter plot + front-side energy spectrum (`fig:ironfoil`) — this is surface-testing data per the text (line 633), so likely exists somewhere outside this report's `figures/` folder
- Front-side composite cluster example with region overlay (`fig:front_composite_example`)
- Back-side composite cluster example with projections (`fig:back_composite_example`)

## Category A.6 — found during execution (2026-07-08)
The document had `\cite{}` commands throughout but **no `\bibliography`/`\bibliographystyle` command anywhere** — so even with a real `references.bib`, nothing would ever have rendered. Missed in the original audit pass; fixed as part of Phase 3 (added `\bibliographystyle{unsrt}` and `\bibliography{references}` before `\end{document}`).

## Category A.7 — found during execution (2026-07-08)
**The document never loaded `amsmath`/`amssymb`**, despite using `\text{}`, `\mathrm{}`, and `\lesssim` throughout (equations in the column-defects and CTI sections, the pressure spec in Set Up). This means the report likely has never compiled cleanly as-is, independent of anything in this audit. Fixed by adding `\usepackage{amsmath}` and `\usepackage{amssymb}` to the preamble.

## Category E — Missing content (real writing needed, not just placeholders)
- **Sec. 5.4 "keV-scale calibration" is completely empty** — just the header, nothing else, immediately followed by "Crosstalk." The intro to Sec. 5.2 (line 638) explicitly promises this: "(i) validate the keV-scale energy calibration and resolution at operating temperature" — that promise is never delivered. This is the single biggest content gap in the report.
- Placeholder citations needing real references: `(CITE GitHub and SSM thesis)` (line 256), `[cite LSST or sequencer]` (line 256), `[cite cdaq]` (line 256).
- Parenthetical aside, line 235: "(here we could include the psd comparison that shows the impat [sic] of the AVC Gen.1)" — either add the PSD comparison figure or cut the aside.
- **No `references.bib` exists for this report at all** — 6 cite keys used (`DAMIC-M:2024ooa`, `DAMIC:2016lrs`, `Gaior:2023wfr`, `Janesick:2001`, `Oscura:2023qik`, `slowcontrol`), zero resolve. `Gaior:2023wfr` is the same ACM reference already stubbed in the sensor paper's bib — can share/cross-reference that stub. The rest need a fresh bibliography.

## Category F — Administrative/metadata gaps
- `\author{}` is entirely commented out (line 99) — report currently compiles with no listed authors.
- `\Operators` = "Diego Venegas-Vargas, ..." — incomplete operator list.
- `\SoftwareTag` = "git: need to update hash / config: 1.32" — literally says it needs updating.
- Document Control table has 3 commented-out rows (Module ID, CCD inventory, Test location) — these macros (`\ModuleID`, `\CCDList`, `\TestLocation`) were written for a single-module report template and don't fit a 28-module summary report. Either repurpose (e.g., "Modules covered: DM01–DM28") or remove.
- Executive summary "Key findings" list has an empty trailing bullet (line 151).
- "Recommended actions" list has only 1 item (DM-11 wire bonding), but "Key findings" lists 3 more notable issues (DM-10 persistent high DC, DM-24/-25/-26 installation-dependent DC, vertical CTI under LBC parameters) with no corresponding action items — worth deciding if these need explicit recommendations or are considered resolved/informational-only.

---

# Comprehensive execution plan (2026-07-08)

Seven phases, ordered by dependency. Phases 1–3 need nothing from anyone and can start immediately on your go-ahead. Phases 4–5 need your input (a few are quick calls, one or two are real content). Phase 6 verifies. Phase 7 is the actual point of all this — closing the loop back to the sensor paper.

## Phase 1 — Structural fixes (zero new input, ready now)
Concrete, not just categorized:
- Add `\label{sec:summary}` to `\section{Summary}` (line 127)
- Add `\label{sec:setup}` to `\section{Set Up}` (line 221)
- Add `\label{sec:procedure}` to `\section{Procedure for data acquisition}` (line 408)
- Add `\label{sec:hightemp}` to `\section{High-temperature image analyses}` (line 471)
- Add `\label{sec:lowtemp}` to `\section{Low-temperature image analysis}` (line 562)
- Add `\label{sec:appendix}` to `\section{Appendix}` (line 845)
- Add `\label{sec:column_defects}` to `\subsection{Column defects in CCD active region}` (line 478) — fixes the dangling `\ref{sec:column_defects}` at line 539
- Fix line 134: `\ref{sec:XXX}--\ref{sec:YYY}` → `\ref{sec:hightemp}--\ref{sec:lowtemp}` (the actual span of sections with per-module detailed metrics: column defects through Crosstalk)
- Fix line 618: `\ref{sec:modulepack}` → `\cite{Traina2025}` (module packaging is Traina et al.'s scope, not this report's — there's no packaging section here to point to internally)
- Fix line 462: add missing period at end of sentence (cosmetic)

## Phase 2 — Wrong-caption fix (needs one quick call, see Phase 4)
Once you confirm which image files are authoritative (current `image31_low_*` vs. the unused `Low_temp_*.png`), fix the three copy-pasted captions on `fig:Single_e_res_all`, `fig:Gain_all`, `fig:DC_all` to actually describe single-electron resolution, gain, and dark current respectively (not "SR defects... module DM18").

## Phase 3 — Bibliography (research, can start now)
Literature search for the 6 missing keys. Working hypotheses to verify:
- `Janesick:2001` — near-certainly Janesick, *Scientific Charge-Coupled Devices*, SPIE Press 2001 (standard CCD reference textbook).
- `Oscura:2023qik` — likely the same Cervantes-Vergara et al. 2023 JINST paper already in the sensor paper's bib as `Oscura2023` — the INSPIRE-style key year matches. If confirmed, just reuse/alias it.
- `Gaior:2023wfr` — same ACM electronics reference already stubbed (incompletely) in the sensor paper's `references.bib` — reuse that stub, then both documents share the fix once the real citation is found.
- `DAMIC:2016lrs` — likely a DAMIC 2016 paper on diffusion/backside-event depth reconstruction, given the surrounding text. Needs a targeted search.
- `DAMIC-M:2024ooa` — likely a DAMIC-M status/slow-control paper, needs search.
- `slowcontrol` — may be an internal tech note or GitHub repo rather than a public citable reference; flag for you to confirm rather than search.

## Phase 4 — Your quick calls (small decisions, ~5 min total)
1. **Figures**: use `Low_temp_SER_modules.png` / `Low_temp_gain_modules.png` / `Low_temp_DC_modules.png`, or keep the current `image31_low_*` files and just fix captions?
2. **Document Control block**: this report covers 28 modules, but `\ModuleID`/`\CCDList`/`\TestLocation` were written for a single-module template. Repurpose (e.g., "Modules covered: DM01–DM28") or remove those rows entirely?
3. **Author list**: currently commented out entirely (line 99, has a candidate list already typed in as a comment — Ávalos, Chavarria, Norcini, Lin, Rosenmerkel, Traina, you, Zhu). Restore as-is, or update?
4. **Operators / software tag**: who besides you operated the test stand (`\Operators`), and what's the current git hash for `\SoftwareTag`?
5. **Recommended actions**: DM-10, DM-24/-25/-26, and the vertical-CTI-under-LBC-parameters findings have no corresponding recommended action (only DM-11 does) — intentional, or should these get explicit follow-up items too?

## Phase 5 — Real content (needs you or the LSM team, not fixable by editing)
- **Sec. 5.4 "keV-scale calibration" — completely empty**, despite being promised in Sec. 5.2's intro. This is the biggest gap. Do you have the analysis/numbers already somewhere (energy calibration is discussed elsewhere in the doc, so this may be partially done), or does it need drafting from scratch?
- **5 missing figures**, no file exists for any of them: chamber exterior photo, module-batch installation photo, iron-foil σ_xy scatter + front-side spectrum (likely exists from surface testing, per the text), front-side composite-cluster example, back-side composite-cluster example.
- PSD comparison figure showing AVC Gen. 1 vs. Gen. 2 impact (line 235 aside) — add the figure, or cut the aside if it's not going to happen.

## Phase 6 — Verification
Recompile with the same siunitx-stubbed method used for the sensor paper, two passes, confirm zero dangling refs and zero undefined-reference warnings (citation-undefined warnings are expected until Phase 3's bibliography is real).

## Phase 7 — Close the loop back to the sensor paper
This is the actual goal. Once the technote is solid:
- Go through every `\TODO` in the sensor paper's `main.tex` that currently says "see LSM report" / cites `LSM_Module_Test_Report` and replace with real drafted text, now citing a report that's internally consistent.
- Priority order, matching what's already closest to done: uniformity (`sec:results:uniformity`), CTI (`sec:results:cti`), cross-talk (`sec:results:crosstalk`), dark-current population baseline (`sec:results:dark`) — all four already pull numbers from this report's Table 1, so they benefit immediately once Phase 1's structural fixes land.
- The keV-scale calibration content from Phase 5 (once written) directly unblocks the sensor paper's `sec:methods:cal` TODO.

---

# What I need from you to proceed
- Green light for Phase 1 (no input needed, purely mechanical) and Phase 3 (bibliography research) — I can start both immediately.
- Answers to the 5 quick calls in Phase 4 whenever convenient — none of them block Phase 1/3.
- Your read on Phase 5's two open content items (keV-calibration status, missing figures) whenever you have it — these are the real bottleneck to a fully solid technote.
