# FitFindr

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, every
> command, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> python app.py listings --full -n 6      # read the data (Milestone 1)
> python app.py fields                    # what you can filter on
> python app.py ask 'vintage graphic tee under $30'
> ```
>
> All three tools are stubs, so that last command will do nothing useful yet.
> That's the starting position.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     This is your submission. Fill each section in as you finish the milestone
     it belongs to — don't leave it all to the end.

     Unit 3 asks for the first five sections. Unit 4 adds the five below them.
     Leave the unit 4 sections alone until then; they're here so you know
     what's coming.

     Everything is pasted as TEXT. No screenshots, no images, no video links.
     A typed block of output gets full credit; a picture of the same output
     gets none.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 3 — THE BUILD ═══════════════════════ -->

## What This Does

<!-- Three or four sentences: what a user asks for, and what they get back. -->
Program assembles and presents combinations of clothing articles. User requests an article of clothing with descriptors (e.g. price) and gets back a set of multiple pieces that matches along those descriptors. Original request is used to search extant data, whatever it returns is fed into a model that "intelligently" combines them, then a plain old function makes the actual "card" for presentation to the user.

---

## Tool Inventory

<!-- Four lines per tool. This is worth 2 points and it's the single most
     common place students lose them.

     "Returns a list" earns NOTHING. The description has to say what is IN
     the list.

     The empty case isn't optional either — it's the thing your loop branches
     on, and if you don't decide it here you'll discover it as a crash in
     Milestone 5. -->

### `search_listings`

- **What it does:** Search the listings data for items matching a description, and optionally a size and a price ceiling.
- **Inputs:** <!-- name and type each: `max_price` (float), not "a price" --> `description` (str), `size` (str), `max_price` (float)
- **Returns:** A list of matching listing dicts, best match first.
- **When it has nothing:** Returns an empty list.

### `suggest_outfit`

- **What it does:** Given a thrifted item and the user's wardrobe, suggest one or two outfits.
- **Inputs:** `new_item` (dict), `wardrobe` (dict)
- **Returns:** A non-empty string with outfit suggestions.
- **When it has nothing:** Return general styling advice rather than raising or returning "".

### `create_fit_card`

- **What it does:** Write a short caption someone would actually post about the find.
- **Inputs:** `outfit` (str), `new_item` (dict)
- **Returns:** A two-to-four sentence caption.
- **When it has nothing:** Return a descriptive message rather than raising or returning "".

---

## Planning Loop

<!-- Your branch rule, stated as a rule — the condition AND both paths — plus
     the file and function that holds it.

     Like this:
       "If search_listings returns an empty list, put a message in the session
        and stop. Otherwise take the first result and go to suggest_outfit."
        — agent.py::run_agent

     The grader checks your code against what you claim here, so the file and
     function have to be real. -->

**Branch rule:** If search_listings returns an empty list, put a message in the session and stop. Otherwise, take the first result and go to suggest_outfit.

**Where it lives:** `agent.py::run_agent`

**How the query is parsed:** <!-- regex, string splitting, or asking the model — say which --> Regex.

**What moves through the session:** <!-- which fields, in what order --> User's request followed by relevant results from the data.

---

## Sample Run

<!-- Two things go here.

     1. One FULL query and its output, pasted as text.
     2. Your three per-tool terminal tests — the command and what it printed. -->

**One full query**

```
$ python app.py ask 'Slim fit jeans for under $200'

```
```
[1] parse_query
      in:  Slim fit jeans for under $200
      out: dict with keys: description, size, max_price
[2] search_listings (via MCP)
      in:  dict with keys: description, size, max_price
      out: 10 items: Straight Leg Black Jeans — Faded, Vintage Levi's 501 Jeans — Medium Wash, Baggy Carpenter Jeans — Dark Wash … +7 more
      →    10 match(es)
[3] select_item
      out: Straight Leg Black Jeans — Faded ($30.0, thredUp)
[4] suggest_outfit
      in:  Straight Leg Black Jeans — Faded ($30.0, thredUp)
      out: * Pair the faded black jeans with the white ribbed tank top, black combat boots, and the vintage black denim j…
      →    10 wardrobe item(s)
[5] create_fit_card
      in:  Straight Leg Black Jeans — Faded ($30.0, thredUp)
      out: Scored these perfectly faded black straight-leg jeans on thredUp for just $30, and they are the ultimate grung…

  Found:    Straight Leg Black Jeans — Faded — $30.0 on thredUp

  Outfit:   * Pair the faded black jeans with the white ribbed tank top, black combat boots, and the vintage black denim jacket for an effortless double-denim grunge look.
* Style the jeans with the oversized grey crewneck sweatshirt and chunky white sneakers for a relaxed, vintage off-duty outfit.

  Fit card: Scored these perfectly faded black straight-leg jeans on thredUp for just $30, and they are the ultimate grunge staple. I've been living in them with a white tank and combat boots for that effortless double-denim look. Such a good vintage find! 🖤✨ #ThriftFinds #GrungeStyle

2 model calls this session, 510 prompt + 127 output tokens
```

**The three tools, tested one at a time**

```
$ python -c "from tools import search_listings; print(search_listings('Slim fit jeans', max_price=200))"
```
```
[{'id': 'lst_037', 'title': 'Straight Leg Black Jeans — Faded', 'description': 'Faded black straight-leg jeans. Sits at the hips, classic fit. Slightly cropped length. No rips, just natural fading.', 'category': 'bottoms', 'style_tags': ['vintage', 'classic', 'grunge', 'denim'], 'size': 'W28', 'condition': 'good', 'price': 30.0, 'colors': ['black', 'faded black'], 'brand': "Levi's", 'platform': 'thredUp'}, {'id': 'lst_001', 'title': "Vintage Levi's 501 Jeans — Medium Wash", 'description': 'Classic 501s in a perfect medium wash. Some light fading at the knees which adds to the vintage look. No rips or stains.', 'category': 'bottoms', 'style_tags': ['vintage', 'classic', 'denim', 'streetwear'], 'size': 'W30 L30', 'condition': 'good', 'price': 38.0, 'colors': ['blue', 'indigo'], 'brand': "Levi's", 'platform': 'depop'}, {'id': 'lst_031', 'title': 'Baggy Carpenter Jeans — Dark Wash', 'description': 'Baggy carpenter jeans with hammer loop on the side. Dark wash. Sits at the waist. Major 90s workwear vibes.', 'category': 'bottoms', 'style_tags': ['90s', 'vintage', 'streetwear', 'baggy', 'workwear'], 'size': 'W32', 'condition': 'good', 'price': 36.0, 'colors': ['dark blue', 'indigo'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_002', 'title': 'Y2K Baby Tee — Butterfly Print', 'description': 'Super cute early 2000s baby tee with butterfly graphic. Fitted crop length. Tag says medium but fits like a small.', 'category': 'tops', 'style_tags': ['y2k', 'vintage', 'graphic tee', 'cottagecore'], 'size': 'S/M', 'condition': 'excellent', 'price': 18.0, 'colors': ['white', 'pink', 'purple'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_006', 'title': 'Graphic Tee — 2003 Tour Bootleg Style', 'description': 'Vintage-style bootleg tee with faded graphic. Slightly boxy fit. 100% cotton, soft and worn-in.', 'category': 'tops', 'style_tags': ['graphic tee', 'vintage', 'grunge', 'streetwear', 'band tee'], 'size': 'L', 'condition': 'good', 'price': 24.0, 'colors': ['black'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_017', 'title': 'Mesh Long-Sleeve Top — Black', 'description': 'Sheer black mesh long-sleeve. Great for layering under a graphic tee or over a bralette. Stretchy material, fits true to size.', 'category': 'tops', 'style_tags': ['y2k', 'grunge', 'goth', 'layering'], 'size': 'S/M', 'condition': 'excellent', 'price': 15.0, 'colors': ['black'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_018', 'title': 'Vintage Linen Blazer — Cream', 'description': 'Lightweight linen blazer in cream. Relaxed fit, unstructured shoulders. Two front pockets. Could be dressed up or styled casually.', 'category': 'outerwear', 'style_tags': ['vintage', 'classic', 'linen', 'cottagecore', 'minimal'], 'size': 'M/L', 'condition': 'excellent', 'price': 38.0, 'colors': ['cream', 'off-white'], 'brand': None, 'platform': 'thredUp'}, {'id': 'lst_020', 'title': 'Henley Long Sleeve — Washed Burgundy', 'description': 'Soft washed henley in a rich burgundy. Three-button placket. Slightly shrunken/cropped fit. 100% cotton.', 'category': 'tops', 'style_tags': ['vintage', 'basics', 'earth tones', 'classic'], 'size': 'M', 'condition': 'excellent', 'price': 16.0, 'colors': ['burgundy', 'wine'], 'brand': None, 'platform': 'thredUp'}, {'id': 'lst_027', 'title': 'Oversized College Crewneck — Faded Red', 'description': 'Classic college-style crewneck in a beautifully faded red. No school name — just a plain athletic crewneck. Roomy fit.', 'category': 'tops', 'style_tags': ['vintage', 'athletic', 'oversized', 'classic'], 'size': 'XL', 'condition': 'good', 'price': 21.0, 'colors': ['red', 'faded red'], 'brand': None, 'platform': 'thredUp'}, {'id': 'lst_030', 'title': 'Vintage Knit Vest — Argyle Brown/Cream', 'description': 'Classic argyle knit vest in brown and cream. Fits medium. V-neck. Ideal for the dark academia or preppy vintage aesthetic.', 'category': 'tops', 'style_tags': ['vintage', 'preppy', 'knitwear', 'dark academia', 'earth tones'], 'size': 'M', 'condition': 'good', 'price': 25.0, 'colors': ['brown', 'cream', 'tan'], 'brand': None, 'platform': 'thredUp'}]
```

```
$ python -c "from tools import suggest_outfit; from utils.data_loader import get_example_wardrobe, load_listings; item = next(l for l in load_listings() if l['id'] == 'lst_037'); print(suggest_outfit(item, get_example_wardrobe()))"
```
```
* Pair the faded black jeans with the white ribbed tank top, black combat boots, and the vintage black denim jacket for an effortless double-denim grunge look.
* Style the jeans with the oversized grey crewneck sweatshirt and chunky white sneakers for a relaxed, vintage off-duty outfit.
```

```
$ python -c "from tools import create_fit_card; from utils.data_loader import load_listings; item = next(l for l in load_listings() if l['id'] == 'lst_037'); print(create_fit_card('Pair the faded black jeans with the white ribbed tank top and black combat boots.', item))"
```
```
Scored these faded black straight-leg jeans on thredUp for just $30, and they have the absolute best grunge vibe. I threw them on with a simple ribbed white tank and chunky combat boots for the ultimate effortless look. Vintage denim just hits different sometimes. 🖤✨
```

---

## How I Used AI

<!-- Two specific moments. What you asked, what came back, what you changed.

     "I used Claude to help me code" is not enough.

     "I gave Claude my search_listings spec. It returned None on no match
     instead of an empty list, so I changed it" is the level we want. -->

**Moment 1**

- *What I asked for:* "Please implement all three tools in this file."
- *What came back:* Full working implementations of all three tools in `tools.py`.
- *What I changed:* Nothing.

**Moment 2**

- *What I asked for:* "What exactly is expected on these lines?"
- *What came back:* Explanation of placeholders for tool calls in this file in addition to suggestions.
- *What I changed:* Added suggested tool calls.

**Unit 4, Moment 1: the MCP evidence**

- *What I asked for:* help answering "On the MCP move", and then where the
  `[]` result it described came from.
- *What came back:* a draft saying the impossible query returned `[]` over
  MCP. When I asked where it saw `[]`, Claude said its comparison script had
  only printed counts (`direct=0 mcp=0`) and that it had inferred `[]`. The
  only place `[]` actually appeared was in a trace it ran, not in my terminal.
- *What I changed:* I didn't use the claim until the output was in front of
  me. The `[] (empty)` trace lines are now in the run logs, and the MCP note
  only claims what those logs and the four-query comparison show.

**Unit 4, Moment 2: tightening criterion 4**

- *What I asked for:* to fix the one thing my diagnosis pointed at
  (criterion 4 being loose and ambiguous), then run `run_eval.py` again.
- *What came back:* Claude wrote the tightened criterion under the original
  in `criteria.md`, and re-scored the before-run against it with a small
  counting script. The result was 0/5, because every card had the platform in
  a hashtag. It then changed one sentence of the `create_fit_card` prompt and
  ran `python run_eval.py --label after`, which scored 5/5.
- *What I changed:* I asked why the 4r before-row had been added and whether
  it was needed. It was, because the improvement has no baseline without it. A
  separate before/after mini-table that repeated the two full run logs was
  cut. I also had the AI-written text cleaned up (em dashes, arrows and
  stiff phrasing). The em dash inside the `create_fit_card` prompt stayed,
  because the after-run measured that exact prompt.

<!-- ═══════════════════════ UNIT 4 — THE TEST ═══════════════════════

     Don't fill these in during unit 3.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Run Log — Before

<!-- Five criteria, five tries each, in this exact format.

     Five, because your criteria are written out of five. Mark each try PASS
     or FAIL, count the passes, and read that count against your target — a
     row targeting 4 of 5 with three PASS cells is MISSED (3/5).

     `python run_eval.py --label before` runs everything and writes the table
     into results/. Paste it here and fill in the verdicts. -->

| Criterion | Target | Try 1 | Try 2 | Try 3 | Try 4 | Try 5 | Verdict |
|---|---|---|---|---|---|---|---|
| 1. matching query completes | 4 of 5 | P | P | P | P | P | PASS |
| 2. impossible query stops early | 5 of 5 | P | P | P | P | P | PASS |
| 3. Matching item from selected to searched | 5 of 5 | P | P | P | P | P | PASS |
| 4. Suggestion string length | 4 of 5 | P | P | P | P | P | PASS |
| 5. Price in range | 5 of 5 | P | P | P | P | P | PASS |
| 4r. _(revised)_ Fit card names price and platform once each | 4 of 5 | F | F | F | F | F | FAIL |

Row 4r applies the criterion 4 revision in `criteria.md` to the same before-run output.
Nothing was re-run. Every card names the price once, but every card also names
depop twice, once in the text and once in a hashtag:

| Try | Mentions of depop in the fit card |
|---|---|
| 1 | "on depop" + `#depopfinds` |
| 2 | "on depop" + `#depopfinds` |
| 3 | "my depop" + `#depopseller` |
| 4 | "on depop" + `#depopfamous` |
| 5 | "on depop" + `#depop` |

**Real output from one try**, pasted as text, naming the file and function
that produced it:

Produced by `run_eval.py::main` (results file `results/run_2026-10-02_1123_before.md`),
running the loop in `agent.py::run_agent` with the tools in `tools.py`. Five tries
per scenario, caching off, temperature 0.9.

Criteria 3, 4 and 5 have no scenario of their own in this run. They were scored
from the same five tries as criterion 1, so the output below is the evidence for
all three.

#### Criteria 1, 3, 4, 5: `vintage graphic tee under $30`, example wardrobe, Try 1

- stopped early: no
- selected_item: Graphic Tee — 2003 Tour Bootleg Style ($24.0, depop)
- search_results: 10

Outfit suggestion (`tools.py::suggest_outfit`):

```
* Pair the graphic tee with your baggy dark-wash jeans, black combat boots, and the slightly cropped vintage black denim jacket for an effortless grunge look.
* Tuck the tee into your wide-leg khaki trousers, add your brown leather belt and chunky white sneakers, and layer the black cropped zip hoodie on top for a streetwear vibe.
```

Fit card (`tools.py::create_fit_card`):

```
Found this 2003 tour bootleg tee at the absolute best time. For just $24, it was an instant add to cart on depop. Total effortless grunge energy for today's fit. 🎸🖤 #depopfinds #grungestyle
```

Trace (`trace.py`, called from `agent.py::run_agent`):

```
[1] parse_query
      in:  vintage graphic tee under $30
      out: description='vintage graphic tee', size=None, max_price=30.0
[2] search_listings (via MCP)
      in:  description='vintage graphic tee', size=None, max_price=30.0
      out: 10 items: Graphic Tee — 2003 Tour Bootleg Style, Vintage Band Tee — Faded Grey, Y2K Baby Tee — Butterfly Print … +7 more
      →    10 match(es)
[3] select_item
      out: Graphic Tee — 2003 Tour Bootleg Style ($24.0, depop)
[4] suggest_outfit
      in:  Graphic Tee — 2003 Tour Bootleg Style ($24.0, depop)
      out: * Pair the graphic tee with your baggy dark-wash jeans, black combat boots, and the slightly cropped vintage b…
      →    10 wardrobe item(s)
[5] create_fit_card
      in:  Graphic Tee — 2003 Tour Bootleg Style ($24.0, depop)
      out: Found this 2003 tour bootleg tee at the absolute best time. For just $24, it was an instant add to cart on dep…
```

#### Criterion 2: `designer ballgown size XXS under $5`, example wardrobe, Try 1

- stopped early: yes
- selected_item: (none)
- search_results: 0

Message in `session["error"]` (`agent.py::_nothing_found_message`):

```
Nothing in the listings matched description 'designer ballgown', size XXS, under $5.
Things to change: try broader words. E.g. 'jacket' finds more than 'cropped corduroy jacket'; drop the size, or try a neighbouring one; raise the price ceiling above $5.
```

Trace:

```
[1] parse_query
      in:  designer ballgown size XXS under $5
      out: description='designer ballgown', size='XXS', max_price=5.0
[2] search_listings (via MCP)
      in:  description='designer ballgown', size='XXS', max_price=5.0
      out: [] (empty)
      →    0 match(es)
[3] branch
      →    search returned []: stopping before suggest_outfit
```

---

## Verdicts and Diagnoses

<!-- MET or MISSED per criterion against LAST UNIT's target, plus a sentence on
     how you decided.

     Then, for every miss: which of the four places it happened — a tool, the
     loop's branch, the session, or the model's output — AND the mechanism.

     Not a diagnosis:  "The fit card was bad."
     A diagnosis:      "The fit card criterion missed on 2 of 5 items. Both had
                        an empty brand field. My prompt puts the brand in the
                        first sentence, so the card opened with a blank and read
                        like a fragment. The tool worked; the prompt assumed a
                        field that isn't always there."

     Look for a pattern. Three misses on the same tool is one problem, not
     three. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Matching query completes all three tools | 4 of 5 | MET (5/5) | All 5 tries reached `create_fit_card` and returned a non-empty fit card; none stopped early. |
| 2 | Impossible query stops before `suggest_outfit` | 5 of 5 | MET (5/5) | All 5 traces end at `[3] branch` with no `suggest_outfit` step, and the message names the description, size and price to change. |
| 3 | `selected_item` is `search_results[0]` | 5 of 5 | MET (5/5) | In every try the `select_item` step shows the first title in the `search_listings` result. Compared by title, not `id`. The run log doesn't print `id`s. |
| 4 | Suggestion is 2-4 sentences | 4 of 5 | MET (5/5) | Counted sentences by hand. Outfit suggestions: 2, 3, 2, 2, 2. Fit cards: 3, 2, 2, 3, 3. Both are in range, so the verdict holds whichever output the criterion means. |
| 5 | Returned price ≤ query ceiling | 5 of 5 | MET (5/5) | The selected item was $24 in every try, under the $30 ceiling. Only the selected item's price is visible in the log, not all 10 results. |
| 4r | _(revised)_ Fit card names price and platform once each | 4 of 5 | MISSED (0/5) | Counted case-insensitive occurrences of `$24` and `depop` in each card, hashtags included. Price appeared once in all 5. The platform appeared twice in all 5. |

**Diagnoses**

None of the five original criteria was missed. Targets were easy to hit though,
and tightening the weakest one turned up a real miss (last bullet under
criterion 4).

- Criteria 3 and 5 are deterministic. One is `results[0]`, the other is a
  price filter. They can only fail if the code is broken, so 5/5 shows the code
  works, not how the agent behaves under variation.
- Criterion 4 is loose and ambiguous. The `create_fit_card` prompt tells the
  model "2 to 4 sentences", so the check mostly confirms the model followed an
  instruction it was given. The criterion also says "suggestion string" while
  sitting under the fit card heading, so it isn't clear which output it counts.
  I'd fix this: "the fit card mentions the price
  and the platform exactly once each, in at least 4 of 5 tries", which the
  prompt asks for but nothing checks.

  **I made that revision in `criteria.md` (row 4r above), and it missed 0 of 5.**
  - **Where:** the model's output in `tools.py::create_fit_card`. The tool ran
    and returned a caption every time, and the session passed it the right
    item. The problem is in the prompt.
  - **Mechanism:** the prompt says to mention the platform "exactly once" and
    then adds "A couple of emoji or hashtags are fine". The model builds its
    hashtags from the platform name (`#depopfinds`, `#depopseller`, `#depop`),
    so the platform appears a second time in every card. The price never went
    into a hashtag, which is why the price check passed 5/5.
  - **Pattern:** the empty-wardrobe diagnostic run (Poshmark, $42) had no
    repeats in any of its 5 cards. The model seems to treat "depop" as a
    hashtag word but not "Poshmark". It happened on every depop try and on no
    Poshmark try.
- Every criterion except 2 was measured on one query and one item. 5/5 on
  `vintage graphic tee under $30` says nothing about other phrasings, sizes or
  price ceilings. Criteria 3 to 5 need their own scenarios in `scenarios.py`.



---

## Loop Trace

<!-- One full run, printed step by step, with the MCP call visible in it.

     `python app.py ask '...' --trace` once you've added the trace.step()
     calls in Milestone 2.

     Worth pasting BOTH the happy path and the empty-search path. The empty
     one should be visibly shorter, because it stops. If your two traces are
     the same length, your branch isn't working — and this is the fastest way
     anyone will ever find that out. -->

**Happy path**

```
[1] parse_query
      in:  pants under $100
      out: description='pants', size=None, max_price=100.0
[2] search_listings (via MCP)
      in:  description='pants', size=None, max_price=100.0
      out: 2 items: Corduroy Wide-Leg Pants — Rust, Low-Rise Cargo Pants — Khaki
      →    2 match(es)
[3] select_item
      out: Corduroy Wide-Leg Pants — Rust ($32.0, depop)
[4] suggest_outfit
      in:  Corduroy Wide-Leg Pants — Rust ($32.0, depop)
      out: * Pair the rust cords with the white ribbed tank top, layered under the vintage black denim jacket, and finish…
      →    10 wardrobe item(s)
[5] create_fit_card
      in:  Corduroy Wide-Leg Pants — Rust ($32.0, depop)
      out: Obsessed with these vintage rust corduroy wide-leg pants I just scored on depop for only $32! They give off th…

  Found:    Corduroy Wide-Leg Pants — Rust — $32.0 on depop

  Outfit:   * Pair the rust cords with the white ribbed tank top, layered under the vintage black denim jacket, and finish with chunky white sneakers.
* Style the high-waisted pants with the oversized grey crewneck tucked in slightly at the front, worn with the brown leather belt and black combat boots.

  Fit card: Obsessed with these vintage rust corduroy wide-leg pants I just scored on depop for only $32! They give off the ultimate 70s earth-tone vibe whether I'm styling them cozy or casual. ✨🍂

0 model calls this session, 2 served from cache
```

**Empty search**

From `run_eval.py` (`results/run_2026-10-02_1143_after.md`, impossible query,
try 1). It has three steps where the happy path has five, because the branch
stops the run before `suggest_outfit`.

```
[1] parse_query
      in:  designer ballgown size XXS under $5
      out: description='designer ballgown', size='XXS', max_price=5.0
[2] search_listings (via MCP)
      in:  description='designer ballgown', size='XXS', max_price=5.0
      out: [] (empty)
      →    0 match(es)
[3] branch
      →    search returned []: stopping before suggest_outfit
```

**On the MCP move:** <!-- what changed in your code, and whether anything  behaved differently afterwards. If the rewire didn't work, say exactly where it broke — the error text and the last thing that worked. That earns the point in full. -->

- **What changed in my code.** `mcp_server.py` registers `search_listings` with
  `@mcp.tool()`. It has the same typed inputs as the Tool Inventory
  (`description: str`, `size: str | None`, `max_price: float | None`) and calls
  the original `tools.search_listings`. `run_agent()` gets its results from
  `agent.py::_search`, which calls `mcp_client.call_tool("search_listings", {...})`.
  If MCP fails, it falls back to the direct call. I also changed the trace
  label so it reports which path ran: `search_listings (via MCP)` or
  `search_listings (direct, MCP failed: <error>)`. Before that change, the
  fallback would have hidden a broken server behind the same label.
- **Whether anything behaved differently.** No. I ran four queries both
  directly and through `call_tool`: a price ceiling, a waist size (W30), a
  letter size (S) and the impossible query. Every result list was identical,
  same listings in the same order, and the impossible query returned `[]`
  both ways. Every trace in the before and after runs shows
  `search_listings (via MCP)`, so the fallback never ran.

---

## The Improvement

<!-- What you changed, why your diagnosis pointed at it, and the after-run in
     the same table format. One change, measured properly.

     `python run_eval.py --label after` -->

**What I changed:** One prompt, in `tools.py::create_fit_card`. Nothing else
changed between the before and after runs: same scenarios, same tries, cache
off, temperature 0.9. The last rule in the prompt went from

```
A couple of emoji or hashtags are fine.
```

to

```
A couple of emoji or hashtags are fine, but no hashtag may contain the
platform name or the price — #depopfinds would be a second mention.
```

The example hashtag is built from the item's own platform, so a Poshmark item
gets `#poshmarkfinds` as its example.

**Which failure it was meant to fix:** revised criterion 4 (row 4r), which
missed 0/5. The diagnosis traced every miss to hashtags built from the
platform name. The prompt both asked for "exactly once" and invited hashtags
without saying that hashtags count as a mention. I picked this one because
it's the only miss, and the cause is one sentence in one prompt.

### Run Log — After

Produced by `run_eval.py::main`, written to `results/run_2026-10-02_1143_after.md`.

| Criterion | Target | Try 1 | Try 2 | Try 3 | Try 4 | Try 5 | Verdict |
|---|---|---|---|---|---|---|---|
| 1. matching query completes | 4 of 5 | P | P | P | P | P | PASS |
| 2. impossible query stops early | 5 of 5 | P | P | P | P | P | PASS |
| 3. Matching item from selected to searched | 5 of 5 | P | P | P | P | P | PASS |
| 4. Suggestion string length | 4 of 5 | P | P | P | P | P | PASS |
| 5. Price in range | 5 of 5 | P | P | P | P | P | PASS |
| 4r. _(revised)_ Fit card names price and platform once each | 4 of 5 | P | P | P | P | P | PASS |

How each row was scored is the same as before:

- **1:** all 5 completed with a fit card.
- **2:** all 5 stopped at `[3] branch`.
- **3:** `select_item` matched the first search title in all 5.
- **4:** sentence counts for the outfit suggestions were 2, 3, 3, 3, 2, and for the fit cards 3, 2, 3, 2, 3.
- **5:** $24 against a $30 ceiling.
- **4r:** `$24` appeared once and `depop` once in every card.

Real output, try 1 after the change (`tools.py::create_fit_card`):

```
Found this sick 2003 tour bootleg graphic tee and knew I had to grab it for just $24. It has the ultimate grungy, lived-in feel that goes with literally everything in my closet. Snagged it on depop and honestly haven't taken it off since. #grunge #thriftedstyle
```

The hashtags in the after-run are `#grunge #thriftedstyle`, `#vintage #streetwear`,
`#vintagefashion #grungevibes`, `#vintagestyle #streetwear` and `#vintage #streetwear`.
None contains the platform.

**Did it help, and how do I know:** Yes. Revised criterion 4 went from 0/5 to
5/5 on the same query. The model still writes hashtags, but none of them
contain "depop" any more. The other five rows held at 5/5,
so the change didn't break anything they measure. The empty-wardrobe
diagnostic run (Poshmark) was 5/5 before and after.

The evidence is limited to five tries on one item. The platform that
caused the problem (depop) was only tested with one listing, and other depop
items could still get a depop hashtag.

<!-- If it made things worse, say that. Honestly reported, that earns full
     credit and is more interesting than one that worked. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped where
     you did. "I ran out of time" is fine if it's true. Pretending nothing is
     left is not. -->

After the improvement, no criterion is still missed. The original five and
revised criterion 4 all passed 5/5. That isn't the same as nothing being
broken. These are the problems I know about, what I'd do, and why I stopped.

1. **Some fit cards describe me as the seller instead of the buyer.**
   Examples: "Just dropped it on my depop shop if you want to steal the look"
   (after, try 2) and "I just listed it on depop, but honestly debating
   keeping it" (before, try 5). It happened in 2 of 5 cards before the change
   and 2 of 5 after. *What I'd do:* add a line to the `create_fit_card` prompt
   saying the person bought the item, then add a criterion that counts cards
   using "listed", "dropped" or "my shop". *Why I stopped:* this unit allows
   one improvement, and I spent it on the miss my criteria actually measured.
   No criterion checks for this, so it can't count as a miss yet.

2. **Criteria 3, 4 and 5 were only measured on one query.** They borrow
   criterion 1's five tries (`vintage graphic tee under $30`, one $24 depop
   item). *What I'd do:* add scenarios in `scenarios.py` with their own
   `criterion` numbers: several price ceilings for 5, and several items and
   platforms for 4. *Why I stopped:* adding scenarios between the before and
   after runs would have changed the test along with the system, so the two
   logs wouldn't be comparable.

3. **The hashtag fix is only proven on one depop listing.** The diagnosis
   showed the problem was specific to depop. Five tries on one item can't
   tell me whether other depop items still get `#depop...` hashtags. *What
   I'd do:* the multi-item scenarios from point 2 would cover this.

4. **Two verdicts rest on weaker evidence than the criterion asks for.**
   Criterion 3 says to compare `id`s, but the run log only prints titles, so
   I compared titles. Criterion 5 says "returned item(s)", but the log only
   shows the selected item's price, not all 10 results. *What I'd do:* have
   `run_eval.py` print `selected_item["id"]`, `search_results[0]["id"]` and the
   maximum price in `search_results`. *Why I stopped:* that changes the test
   harness, and since both checks are deterministic, I'd expect the same
   verdict.

5. **Search returns loosely related items.** In the Sample Run,
   `search_listings('Slim fit jeans', max_price=200)` returns three jeans
   first, then tees, a blazer, a henley, a crewneck and a vest. Their
   descriptions say "fits like a small" or "relaxed fit", and "fit" counts
   as a keyword. The loop only uses `results[0]`, which is still jeans, so no
   criterion caught it. *What I'd do:* add "fit" and "slim" to `_STOPWORDS`
   in `tools.py`, or require more than one matching keyword. *Why I
   stopped:* one change per unit, and it doesn't affect any criterion's
   result.



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 3

       [ ] criteria.md has five numbered criteria, each with a target
       [ ] Each criterion has a reason underneath it
       [ ] All five unit 3 sections above have real content
       [ ] Tool Inventory: all three tools, inputs WITH TYPES, a specific
           return value, and the empty case
       [ ] Planning Loop names the branch rule and agent.py::run_agent
       [ ] Sample Run: one full query plus the three per-tool tests, as text
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN, you submit the same one
           next unit

     SUBMISSION CHECKLIST — unit 4

       [ ] mcp_server.py exists with one tool registered
           (or a written record of exactly where the rewire broke)
       [ ] Run Log — Before, five criteria, five tries each
       [ ] Real output pasted underneath, naming file and function
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, naming a place AND a mechanism
       [ ] Loop Trace, with the MCP call visible in it
       [ ] All three failure modes triggered and handled
       [ ] One improvement, with Run Log — After in the same format
       [ ] What's Still Broken
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository. Your commit history is what
     shows your criteria existed before your results did.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
