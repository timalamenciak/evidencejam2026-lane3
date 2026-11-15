# Whimsy · Restoration Evidence

**Explore what restoration studies found, how certain the evidence is, and where research is missing.**

Built for **Evidence Jam 2026 (Causapalooza!) · Lane 3 — Decision Support**.

Whimsy helps Canadian restoration practitioners, researchers, and funders explore evidence about tree planting, created marshes, and grassland restoration. Start with a plain-language answer, then open a finding to see its direction, certainty, study count, and the original sentences from the papers.

[Explore the questions](#three-questions) · [Try a finding](#try-a-finding) · [Run locally](#quick-start) · [Figma wireframes][figma] · [Answer pack](answers/answers.md)

<img width="900" alt="Whimsy restoration evidence explorer — project screenshot" src="https://github.com/user-attachments/assets/eb1cbdd0-6b1c-4929-a6c3-c076cc02ee19" />

## Three questions

| Question | What you can explore |
| --- | --- |
| **01 · What helps planted seedlings survive and grow?** | Compare planting stock and site treatments through 13 findings drawn from 239 claims in 29 papers. Follow the causal graph to inspect the evidence behind individual relationships. |
| **02 · How long until a created marsh matches a natural one?** | Explore eight comparisons with natural marshes and three trends with marsh age. The available evidence does **not establish a time to recovery**. |
| **03 · Where is grassland restoration research missing?** | Explore a 24 × 16 evidence map: 123 of 384 intervention–outcome pairings were studied, including 72 covered by just one study. The other 261 are marked **Not studied**. |

Start on **Home** (`index.html`) to choose a question. The **Guide** (`method.html`) explains the symbols, certainty rules, sources, and evidence limitations.

## What makes it useful

- **Answers you can trace.** Open findings to see source sentences grouped by paper, with DOI links where available.
- **Certainty you can inspect.** Each grade comes with its reasoning; a single study is capped at Moderate.
- **Gaps stay visible.** “Not studied” is kept distinct from tested results such as “No effect” and “Mixed.”
- **Works offline after building.** Pages, fonts, images, scripts, and source excerpts are bundled locally. Publisher links require a connection.
- **Multiple ways to explore.** Finding cards, a causal graph, a marsh timeline, and a filterable gap map support different questions.

### Try a finding


1. Open **Q1 → Findings → Seedling survival → Site preparation**. The direction is **Mixed**, the certainty is **High**, and the finding draws on **4 studies**.
2. Read the certainty checklist and excerpts. Select **All 4 papers, 12 quotes** to see the original sentences grouped by paper, with DOI links where available.

In **Q3**, select a numbered cell to see the finding behind it. Open **See not-studied pairings** and filter by topic or outcome to explore gaps.

## Quick start

From the repository root, run the build with Bash and Python 3.9 or later. The build uses only the Python standard library, with no third-party packages to install or pin. [requirements.txt](requirements.txt)

```bash
./build.sh site/
python3 -m http.server 8000 --directory site/
```

Open [http://localhost:8000](http://localhost:8000). The build recreates `site/` on each run. Its entry point is `site/index.html`. Styles, scripts, fonts, images, and source excerpts are bundled for offline use. DOI links open the publishers’ sites when you have a connection.

The build makes no network calls except to the LLM endpoint, and only when all three `BRINE_*` variables are set.

| Build flag | Behaviour |
| --- | --- |
| `--no-llm` | Force template summaries and skip the model endpoint, even if `BRINE_*` variables are set. |
| `--strict` | Return an error if the built-in check finds a discrepancy. |

## Screenshots

A selection of the project screenshots supplied with this repository.

<img width="900" alt="Whimsy interface — project screenshot 2" src="https://github.com/user-attachments/assets/c30fec5a-e5b1-4cd2-90f8-3c3384f6b8f5" />

<img width="900" alt="Whimsy interface — project screenshot 3" src="https://github.com/user-attachments/assets/4d92d42b-c6fd-44ed-924a-77fcff1f27e4" />

<img width="900" alt="Whimsy interface — project screenshot 4" src="https://github.com/user-attachments/assets/b884698f-fe59-45ef-8194-728c3cf13f17" />

## Evidence limits

- **Check the source sentence when context matters.** In Q1, one inoculation claim is coded as an increase in seedling survival. The source sentence reports unchanged survival at most sites and an increase at one. Open **Mycorrhizal or soil inoculation** under **Seedling survival** and select **All 2 papers, 4 quotes** to see the exception.
- **The marsh timeline shows when researchers measured.** Q2 draws on five papers, mostly observational, and pools created and restored marshes. A lag at one observed age cannot establish a recovery trajectory.
- **The grassland map covers the grouped evidence.** Ecosystem tags set the map’s scope, and 287 of the 950 grassland claims fall outside the mapped groups. A frequently studied pairing can still have mixed findings.
- **Keyword matching shapes the pack.** Claim selection and grouping can miss unusually worded claims. Each question page gives the limits for its own evidence.

<details>
<summary>How to read certainty, study coverage, and graph relationships</summary>

- **Certainty has a reason.** The site displays the pack’s grade alongside the finding and shows the rationale in the details. The rule starts with the strongest study design and adjusts for single-study evidence, study agreement, comparators, and evidence about how the intervention works. A single study is capped at **Moderate**.
- **Study coverage and results answer different questions.** **Not studied** marks a pairing the corpus did not cover. **No effect** and **Mixed** describe tested results. The number in each Q3 cell measures study coverage. Q2 uses **Below reference**, **Comparable**, and **Above reference** for comparisons with natural marshes.
- **Each claim leads back to a paper.** A finding’s `edge_ids` identify the original sentences, paper titles, years, and DOIs where supplied. Source panels show the authors’ wording separately from the pack’s certainty grade.
- **Each graph arrow has its own evidence.** A route through an intermediate condition joins distinct bodies of evidence, often from different papers. A moderation link points to the arrow it changes, and the graph shows the members of a combined treatment.

</details>

## Accessibility

Finding cards pair direction symbols with words, such as **↑ Increase** and **⇅ Mixed**. The graph has text labels, keyboard-reachable arrow links with accessible names, and a full list of arrows in a table. The site uses semantic headings, labelled tables, a skip link, and visible keyboard focus. The [browser test suite](tests/a11y/a11y.mjs) checks axe-core rules, page overflow at 375 px, and keyboard navigation.

## Technical details

<details>
<summary>Build pipeline</summary>

1. **Load and validate.** [`build/load.py`](build/load.py) checks the answer JSON, direction and certainty vocabularies, evidence references, and agreement between the Q1 findings and their matching graph edges.
2. **Summarize.** [`build/summarize.py`](build/summarize.py) builds plain-language text from the answer data. An optional model can write the home-page summaries.
3. **Render.** [`build/render.py`](build/render.py) writes the question pages, Guide, and source pages as static HTML.
4. **Draw the graph.** [`build/graph_svg.py`](build/graph_svg.py) lays out treatments, intermediate conditions and combinations, and outcomes in three top-to-bottom tiers. Python computes the positions and draws the graph as inline SVG with text labels.
5. **Check the output.** [`build/check.py`](build/check.py) compares rendered values with the pack, checks local links, and verifies the published source claims.

</details>

<details>
<summary>Optional AI summaries: configuration and safeguards</summary>

The site uses templates by default. Set all three environment variables before running the build to enable model-written home-page summaries. [.env.example](.env.example)

| Variable | Purpose |
| --- | --- |
| `BRINE_URL` | Shared LiteLLM endpoint |
| `BRINE_MODEL` | Model name supplied by the organizers |
| `BRINE_API_KEY` | API key supplied to the build environment |

The build sends one short fact sheet per question, with a 160-token output cap per request. It screens replies for numbers absent from the fact sheet, year or month wording, misleading descriptions of evidence gaps, markup, excessive length, and direction or certainty wording that conflicts with the facts. Request failures and rejected replies fall back to templates.

Accepted summaries carry a build-time AI label on Home. The answer pages, evidence values, graph, and source quotes are generated from the pack. Visitors read the generated files offline.

</details>

<details>
<summary>Verification commands and recorded build measurements</summary>

```bash
./build.sh site/ --no-llm --strict
python3 build/check.py site/
python3 tests/check_independent.py site/
SITE_DIR=site python3 -m unittest discover -s tests -v
```

A clean build of [`1695ab9`][verified-commit] produced **182 HTML pages** in **1.13 seconds** on one Apple silicon Mac running Python 3.12.3. It used **0 model tokens**, reported **0 built-in check problems**, passed **75,620 independent checks**, and passed all **68 unit tests**. The matching GitHub Actions run passed Linux x86-64 and ARM64 builds, a network-disabled build, and browser accessibility checks. [GitHub Actions run][main-ci]

| View | Uncompressed HTML | Estimated uncached assets, including HTML |
| --- | ---: | ---: |
| Home | 49.2 KiB | 261.6 KiB |
| Q1 | 93.8 KiB | 357.2 KiB |
| Q1 graph | 120.7 KiB | 296.5 KiB |
| Q2 | 71.3 KiB | 279.8 KiB |
| Q3 | 362.5 KiB | 612.3 KiB |
| Guide | 11.9 KiB | 153.4 KiB |

Asset estimates sum uncompressed sizes of the page and its local assets, including fonts. The build time is a single local measurement. Live-endpoint token use, DGX Spark energy, and Lighthouse scores were outside that measurement.

</details>

<details>
<summary>Add another question</summary>

Add a file such as `answers/q4_example.json` and rebuild. Use a unique filename prefix and stable finding IDs. Start from an existing answer with question text, `how_built`, `limitations`, `findings`, `gaps`, and an `evidence` map keyed by `edge_ids`.

The builder selects a gap map, comparison, effects page, or generic page from the supplied fields. It detects reference-style findings from `direction_meaning`. A matching `q4_*_graph.json` adds a graph page. The [Q4 reuse test](tests/test_checkers.py) copies Q2 under a new prefix and checks that the home page links to `q4.html` with the comparison timeline and source links.

</details>

<details>
<summary>Page inventory and evidence counts</summary>

| Question | Who uses it | What the site shows |
| --- | --- | --- |
| **Q1 · Tree planting** · `q1.html` | Foresters, planting contractors, and nursery managers | How planting stock and site treatments affect seedling survival and growth. There are 13 findings from 239 claims in 29 papers, one gap, and five claims about combined treatments. |
| **Q1 · Causal graph** · `q1-graph.html` | Practitioners tracing how treatments affect outcomes | A graph with 12 nodes, 29 evidence-backed arrows, and one labelled gap. Select an arrow to see its evidence, or browse the matrix and the table of all arrows. |
| **Q2 · Created marshes** · `q2.html` | Wetland managers and planners | Eight comparisons with natural reference marshes and three trends with marsh age. The page opens with the finding that the time to reach natural reference conditions is unknown in this corpus. |
| **Q3 · Grassland gaps** · `q3.html` | Practitioners, funders, and researchers setting research priorities | A 24 × 16 map of interventions and outcomes. Of 384 pairings, 123 were studied, including 72 by a single study. The other 261 are marked **Not studied**. |

Start on **Home** (`index.html`) to choose a question. Open the **Guide** (`method.html`) for the symbols, certainty rules, data sources, and each question’s limitations.

</details>

## Figma wireframes

[Open the team’s Figma file][figma] to explore the design.

<details>
<summary>Individual frames and implementation notes</summary>

| Screen or interaction | Figma frames | Implementation notes |
| --- | --- | --- |
| Home | [Home and question navigation][figma-home] | [Home layout][home-pr] · [Problem opening][summary-pr] |
| Finding cards | [Desktop][figma-card] · [Phone][figma-card-phone] | [Finding details and sources][cards-pr] |
| Q1 tree planting | [Question page][figma-q1] · [Desktop wireframe][figma-q1-desktop] · [Phone wireframe][figma-q1-phone] | [Question-page design][hero-pr] |
| Q1 causal graph | [Graph and card handoff][figma-q1-graph] | [Graph-first design proposal][graph-pr] |
| Q2 created marshes | [Question page][figma-q2] · [Timeline][figma-q2-graph] · [Evidence desktop][figma-marsh] · [Evidence phone][figma-marsh-phone] | [Timeline interactions][timeline-pr] |
| Q3 grassland gaps | [Question page][figma-q3] · [Desktop wireframe][figma-q3-desktop] · [Phone wireframe][figma-q3-phone] · [Gap-map studies][figma-q3-maps] · [Design C][figma-q3-map-c] | [Gap-map redesign][summary-pr] |
| Not studied states | [Dropdown and gap states on the Q1 frame][figma-q1] | [Storyboard notes and filter behaviour][gaps-pr] |

</details>

## Credits and licences

Built by **Team Whimsy** for Evidence Jam 2026, Lane 3.

- Valavan Marakathalingasivam
- Winnie Chen
- Kaitlyn Chan
- Brian Huang

Thanks to the organizers and mentors. [Repository contributors][contributors]

The [Lane 3 answer pack][upstream] draws on the Lane 2 CAMO causal graph, which contains 5,554 claims from 293 papers. The original pack files in `answers/`, `graph/`, and `groups/` are preserved.

Code is released under the MIT Licence. See [LICENSE](LICENSE).

The bundled fonts use the SIL Open Font License. [Inter licence](static/fonts/inter-LICENSE.txt) · [Newsreader licence](static/fonts/newsreader-LICENSE.txt)

[figma]: https://www.figma.com/design/JmqB89eWc03xvRTxmYFRG5/evidence-jam-causapalooza?node-id=0-1
[figma-home]: https://www.figma.com/design/JmqB89eWc03xvRTxmYFRG5/evidence-jam-causapalooza?node-id=279-3223
[figma-card]: https://www.figma.com/design/JmqB89eWc03xvRTxmYFRG5/evidence-jam-causapalooza?node-id=96-7838
[figma-card-phone]: https://www.figma.com/design/JmqB89eWc03xvRTxmYFRG5/evidence-jam-causapalooza?node-id=96-7529
[figma-q1]: https://www.figma.com/design/JmqB89eWc03xvRTxmYFRG5/evidence-jam-causapalooza?node-id=37-128
[figma-q1-desktop]: https://www.figma.com/design/JmqB89eWc03xvRTxmYFRG5/evidence-jam-causapalooza?node-id=96-8161
[figma-q1-phone]: https://www.figma.com/design/JmqB89eWc03xvRTxmYFRG5/evidence-jam-causapalooza?node-id=96-8927
[figma-q1-graph]: https://www.figma.com/design/JmqB89eWc03xvRTxmYFRG5/evidence-jam-causapalooza?node-id=278-1500
[figma-q2]: https://www.figma.com/design/JmqB89eWc03xvRTxmYFRG5/evidence-jam-causapalooza?node-id=111-90
[figma-q2-graph]: https://www.figma.com/design/JmqB89eWc03xvRTxmYFRG5/evidence-jam-causapalooza?node-id=197-3
[figma-marsh]: https://www.figma.com/design/JmqB89eWc03xvRTxmYFRG5/evidence-jam-causapalooza?node-id=184-2329
[figma-marsh-phone]: https://www.figma.com/design/JmqB89eWc03xvRTxmYFRG5/evidence-jam-causapalooza?node-id=184-2443
[figma-q3]: https://www.figma.com/design/JmqB89eWc03xvRTxmYFRG5/evidence-jam-causapalooza?node-id=143-234
[figma-q3-desktop]: https://www.figma.com/design/JmqB89eWc03xvRTxmYFRG5/evidence-jam-causapalooza?node-id=96-6004
[figma-q3-phone]: https://www.figma.com/design/JmqB89eWc03xvRTxmYFRG5/evidence-jam-causapalooza?node-id=96-5391
[figma-q3-maps]: https://www.figma.com/design/JmqB89eWc03xvRTxmYFRG5/evidence-jam-causapalooza?node-id=211-26
[figma-q3-map-c]: https://www.figma.com/design/JmqB89eWc03xvRTxmYFRG5/evidence-jam-causapalooza?node-id=211-1503
[verified-commit]: https://github.com/winnie-c8/evidencejam2026-whimsy/commit/1695ab9
[main-ci]: https://github.com/winnie-c8/evidencejam2026-whimsy/actions/runs/43750859437
[upstream]: https://github.com/timalamenciak/evidencejam2026-lane3
[contributors]: https://github.com/winnie-c8/evidencejam2026-whimsy/graphs/contributors
[home-pr]: https://github.com/winnie-c8/evidencejam2026-whimsy/pull/76
[cards-pr]: https://github.com/winnie-c8/evidencejam2026-whimsy/pull/51
[hero-pr]: https://github.com/winnie-c8/evidencejam2026-whimsy/pull/81
[graph-pr]: https://github.com/winnie-c8/evidencejam2026-whimsy/pull/78
[timeline-pr]: https://github.com/winnie-c8/evidencejam2026-whimsy/pull/72
[gaps-pr]: https://github.com/winnie-c8/evidencejam2026-whimsy/pull/80
[summary-pr]: https://github.com/winnie-c8/evidencejam2026-whimsy/pull/82

