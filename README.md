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

FitFindr is an agent that lets a user search for thrifted clothes based on a description, size, and max price. It searches the listings, picks the best matching item, and suggests outfits using the user's wardrobe. Then it creates a fit-card caption based on the selected item and outfit. If there is no match, it just gives some advice to the user on what they could change about their search.



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

- **What it does:** Searches all available listings for any items that match the requested size, description, and maximum price. 
- **Inputs:** <!-- name and type each: `max_price` (float), not "a price" --> max_price(float | None), size (str | None), description (str)
- **Returns:** A list of listing dictionaries, each with title, id, description, category, style_tags, size, condition, colors, brand, price, and platform. 
- **When it has nothing:** Returns an empty list.

### `suggest_outfit`

- **What it does:** Suggests an outfit based on the selected listing with items from the wardrobe.
- **Inputs:** new_item (dict), wardrobe (dict)
- **Returns:** A string containing a new outfit based on the new item and compatible pieces from the wardrobe.
- **When it has nothing:** Returns general styling advice for the new item.

### `create_fit_card`

- **What it does:** Creates a short caption describing the new item and suggested outfit.
- **Inputs:** new_item (dict), outfit (str)
- **Returns:** A string containing a short fit card caption that someone could use.
- **When it has nothing:** Returns a short message stating there is not enough information to make the fit card.

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

**Branch rule:** If search_listings returns an empty list, put a message in the session telling the user what they could change about their search and stop. Otherwise, take the first search result, store it as the selected item, and go to suggest_outfit.

**Where it lives:** `agent.py::run_agent`

**How the query is parsed:** <!-- regex, string splitting, or asking the model — say which -->
I used regex to get the size and max price from the user query. The rest of the query becomes the description for the listing search.

**What moves through the session:** <!-- which fields, in what order --> First the query and parsed values are stored, then the search results and selected item. The item is used to create an outfit suggestion, which is stored in the session before creating and storing the final fit card.

---

## Sample Run

<!-- Two things go here.

     1. One FULL query and its output, pasted as text.
     2. Your three per-tool terminal tests — the command and what it printed. -->

**One full query**

```
$ python app.py ask 'vintage graphic tee under $30'

Found:    Y2K Baby Tee — Butterfly Print — $18.0 on depop

Outfit:   **Outfit 1: Casual Y2K Streetwear**
Pair the butterfly baby tee with your **baggy straight-leg jeans** (dark wash) and **chunky white sneakers**. Throw the **black cropped zip hoodie** over your shoulders or layer it on top, and finish with the **black crossbody bag** for an easy, everyday 2000s look.

**Outfit 2: Edgy Contrast**
Tuck the baby tee into your **wide-leg khaki trousers** and wear it with the **black combat boots**. Add the **vintage black denim jacket** on top to balance out the sweet butterfly print with some grunge texture.

Fit card: This Y2K butterfly baby tee is giving total early 2000s street style vibes and is up on my Depop for just $18. Grab it to complete your next casual outfit!

```

**The three tools, tested one at a time**

```
$ python -c "from tools import search_listings; print([(x['title'], x['price']) for x in search_listings('graphic tee', max_price=30)])"

[('Y2K Baby Tee — Butterfly Print', 18.0), ('Graphic Tee — 2003 Tour Bootleg Style', 24.0), ('Mesh Long-Sleeve Top — Black', 15.0), ('Vintage Band Tee — Faded Grey', 19.0), ('Low-Rise Cargo Pants — Khaki', 27.0), ('Vintage Graphic Hoodie — Faded Black', 26.0)]
```

```
$ python -c "from tools import suggest_outfit; from utils.data_loader import get_example_wardrobe, load_listings; print(suggest_outfit(load_listings()[0], get_example_wardrobe()))"

**Outfit 1: Casual Streetwear**
Pair the vintage 501s with the **white ribbed tank top** and the **black cropped zip hoodie** layered over top. Finish the look with the **chunky white sneakers** and the **black crossbody bag** for an easy, everyday outfit.

**Outfit 2: Cozy & Classic**
Tuck the **oversized grey crewneck sweatshirt** into the jeans, accent with the **brown leather belt**, and wear them with the **black combat boots** and the **vintage black denim jacket** thrown over your shoulders.
```

```
$ python -c "from tools import create_fit_card; from utils.data_loader import load_listings; print(create_fit_card('jeans and white sneakers', load_listings()[0]))"

Rocking these vintage Levi's 501 jeans with fresh white sneakers for the ultimate effortless streetwear vibe. Snagged this medium wash pair for just $38.00 on depop and they fit like a dream!
```

---

## How I Used AI

<!-- Two specific moments. What you asked, what came back, what you changed.

     "I used Claude to help me code" is not enough.

     "I gave Claude my search_listings spec. It returned None on no match
     instead of an empty list, so I changed it" is the level we want. -->

**Moment 1**

- *What I asked for:* I asked an LLM to help me understand the `search_listings` function and discussed some approaches.
- *What came back:* It mentioned how a simple substring wouldn't work, but a split and filter method would be better. It also helped me realize we can use regex to handle sizes better.
- *What I changed:* I kept the overlapping keyword approach but added better size matching and regex. 

**Moment 2**

- *What I asked for:* I asked an LLM to help debug and proofread my `run_agent` function. I was running into some errors.
- *What came back:* It helped me realize I was using a version from another example, which turned out to be from the Unit 4 example.
- *What I changed:* I reverted back to the correct starter for this project, then updated it with my logic. And it worked fine after that.

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
| 1. Matching query completes all three tools | 4 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 2. Impossible query stops before suggest_outfit | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 3. Selected item ID matches item passed to suggest_outfit | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 4. Fit card mentions selected item and is at most 2 sentences | 4 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 5. Search results respect maximum price | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |

**Real output from one try**, pasted as text, naming the file and function
that produced it:

**Criterion 1 — Matching query completes all three tools**

Source: `run_eval.py::run_once`, `agent.py::run_agent`

```text
- stopped early: no
- selected_item: Y2K Baby Tee — Butterfly Print ($18.0, depop)
- search_results: 10

[2] search_listings (via MCP)
      in:  {'description': 'vintage graphic tee', 'size': None, 'max_price': 30.0}
      out: 10 items: Y2K Baby Tee — Butterfly Print, Graphic Tee — 2003 Tour Bootleg Style, Vintage Band Tee — Faded Grey … +7 more
[3] select_item
      in:  item_id=lst_002
      out: Y2K Baby Tee — Butterfly Print ($18.0, depop)
[4] suggest_outfit
      in:  dict with keys: new_item, wardrobe
      out: Here are two easy ways to style the baby tee with what you already own:  **Outfit 1: Y2K Streetwear** Pair the…
[5] create_fit_card
      in:  dict with keys: outfit, new_item
      out: Channeling total Y2K streetwear and grunge vibes with this adorable butterfly baby tee, available on Depop for…
```

**Criterion 2 — Impossible query stops before suggest_outfit**

Source: `run_eval.py::run_once`, `agent.py::run_agent`

```text
- stopped early: yes — I couldn't find anything that matched that. Try a broader search, a different size, or a higher price.
- selected_item: (none)
- search_results: 0

[1] parse_query
      in:  designer ballgown size XXS under $5
      out: dict with keys: description, size, max_price
[2] search_listings (via MCP)
      in:  {'description': 'designer ballgown', 'size': 'XXS', 'max_price': 5.0}
      out: [] (empty)
[3] branch
      in:  dict with keys: search_results
      out: I couldn't find anything that matched that. Try a broader search, a different size, or a higher price.
```

**Criterion 3 — Selected item ID matches item passed to suggest_outfit**

Source: `run_eval.py::run_once`, `agent.py::run_agent`

```text
- stopped early: no
- selected_item: Y2K Baby Tee — Butterfly Print ($18.0, depop)
- search_results: 10

[3] select_item
      in:  item_id=lst_002
      out: Y2K Baby Tee — Butterfly Print ($18.0, depop)
[4] suggest_outfit
      in:  dict with keys: new_item, wardrobe
      out: Here are two easy ways to style the Y2K baby tee with what you already own:  **1. Casual Streetwear Vibe** *  …
```

**Criterion 4 — Fit card mentions selected item and is at most 2 sentences**

Source: `run_eval.py::run_once`, `agent.py::run_agent`, `tools.py::create_fit_card`

```text
- stopped early: no
- selected_item: Y2K Baby Tee — Butterfly Print ($18.0, depop)

Fit card:

Scored this cute Y2K butterfly baby tee for just $18 on depop, and I'm obsessed with the effortless streetwear vibe. It's giving major early-2000s nostalgia whether I style it with baggy denim or crisp trousers!
```

**Criterion 5 — Search results respect maximum price**

Source: `tools.py::search_listings`, through `mcp_client.call_tool`

```text
[('Y2K Baby Tee — Butterfly Print', 18.0),
 ('Vintage Band Tee — Faded Grey', 19.0),
 ('Mesh Long-Sleeve Top — Black', 15.0),
 ('Oversized Crewneck Sweatshirt — Vintage Navy', 20.0),
 ('Leather Belt — Brown, Braided', 12.0),
 ('Henley Long Sleeve — Washed Burgundy', 16.0),
 ('Vintage Polo Shirt — Forest Green', 18.0),
 ('Bucket Hat — Reversible, Brown Plaid', 14.0)]
All within budget: True
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

Command: `python app.py ask 'vintage graphic tee under $30' --trace`

```text
[1] parse_query
      in:  vintage graphic tee under $30
      out: dict with keys: description, size, max_price
[2] search_listings (via MCP)
      in:  {'description': 'vintage graphic tee', 'size': None, 'max_price': 30.0}
      out: 10 items: Y2K Baby Tee — Butterfly Print, Graphic Tee — 2003 Tour Bootleg Style, Vintage Band Tee — Faded Grey … +7 more
[3] select_item
      in:  item_id=lst_002
      out: Y2K Baby Tee — Butterfly Print ($18.0, depop)
[4] suggest_outfit
      in:  dict with keys: new_item, wardrobe
      out: **Outfit 1: Casual Y2K Streetwear** Pair the butterfly baby tee with your **baggy straight-leg jeans** (dark w…
[5] create_fit_card
      in:  dict with keys: outfit, new_item
      out: This Y2K butterfly baby tee is giving total early 2000s street style vibes and is up on my Depop for just $18.…
```

**Empty search**

Command: `python app.py ask 'designer ballgown size XXS under $5' --trace`

```text
[1] parse_query
      in:  designer ballgown size XXS under $5
      out: dict with keys: description, size, max_price
[2] search_listings (via MCP)
      in:  {'description': 'designer ballgown', 'size': 'XXS', 'max_price': 5.0}
      out: [] (empty)
[3] branch
      in:  dict with keys: search_results
      out: I couldn't find anything that matched that. Try a broader search, a different size, or a higher price.
```

**On the MCP move:** <!-- what changed in your code, and whether anything
behaved differently afterwards. If the rewire didn't work, say exactly where it
broke — the error text and the last thing that worked. That earns the point in
full. -->
I moved `search_listings` onto mcp by adding it to the `mcp_server` with a description and inputs. I replaced the direct function call with the mcp call and changed the input shape to match. I tested the tool the same way and everything worked as it did before the change. The only difference is taht the agent uses an MCP server to call `search_listings` instead of calling it directly.



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
