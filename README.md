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
| 1.  |  |  |  |  |  |  |  |
| 2.  |  |  |  |  |  |  |  |
| 3.  |  |  |  |  |  |  |  |
| 4.  |  |  |  |  |  |  |  |
| 5.  |  |  |  |  |  |  |  |

**Real output from one try**, pasted as text, naming the file and function
that produced it:

```

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
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**



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

```

**Empty search**

```

```

**On the MCP move:** <!-- what changed in your code, and whether anything
behaved differently afterwards. If the rewire didn't work, say exactly where it
broke — the error text and the last thing that worked. That earns the point in
full. -->



---

## The Improvement

<!-- What you changed, why your diagnosis pointed at it, and the after-run in
     the same table format. One change, measured properly.

     `python run_eval.py --label after` -->

**What I changed:**

**Which failure it was meant to fix:**

### Run Log — After

| Criterion | Target | Try 1 | Try 2 | Try 3 | Try 4 | Try 5 | Verdict |
|---|---|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |  |  |
| 2.  |  |  |  |  |  |  |  |
| 3.  |  |  |  |  |  |  |  |
| 4.  |  |  |  |  |  |  |  |
| 5.  |  |  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it made things worse, say that. Honestly reported, that earns full
     credit and is more interesting than one that worked. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped where
     you did. "I ran out of time" is fine if it's true. Pretending nothing is
     left is not. -->



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
