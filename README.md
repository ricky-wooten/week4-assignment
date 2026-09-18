# Red Robin Nutrition Nest — Rules-Based Chatbot

**Course:** Foundations of AI (M.S. Applied AI)
**Assignment:** Week 4
**Author:** Ricky Wooten
**Platform:** Built and tested in [BoodleBox](https://boodlebox.ai)

**Try the chatbot:** [https://box.boodle.ai/a/@NutritionNest](https://box.boodle.ai/a/@NutritionNest)

## Overview

This project is a rules-based chatbot that answers customer questions about Red
Robin's core menu — burgers, sides, and salads — using nutrition and dietary data
pulled directly from Red Robin's official published nutrition guide. The chatbot
follows explicit IF/THEN rules rather than open-ended reasoning, so its behavior is
predictable and traceable back to a defined rulebook.

## Files in This Repository

| File | Purpose |
|---|---|
| `redrobin_core_menu.csv` | The reference data: 129 menu items (burgers, buns, protein patties, cheese toppings, sides, dipping sauces, salads, soups, and salad dressings) with full nutrition facts (calories, fat, sodium, carbs, protein, etc.) and dietary flags (`vegetarian`, `vegan`, `gluten_free`). |
| `redrobin_chatbot_system_prompt.txt` | The rulebook: a plain-text system prompt defining exactly how the chatbot should respond to different types of customer questions. Pasted directly into BoodleBox's bot builder. |
| `CLAUDE.md` | Standing context/instructions used with Claude throughout development (background, working style, SDLC guidance). Not part of the chatbot itself — this documents the *process* used to build it. |

## Data Source

Nutrition data was transcribed from Red Robin's official nutritional guide PDF
(`redrobin.com`, live date 10/02/2023), covering the Burgers, Veggie & More, Tavern
Burgers, Substitutions & Sides, Dipping Sauces, Salads & Soups, and Salad Dressings
sections. The catering menu and alcohol/dessert/beverage sections were intentionally
excluded to keep the first version of this chatbot focused on the core in-restaurant
menu.

## Important Limitation: Inferred Dietary Flags

The source PDF provides precise nutrition numbers for every item, but it does **not**
include an official vegan/vegetarian/gluten-free label column. The `vegetarian`,
`vegan`, and `gluten_free` columns in the CSV are **my own best-effort inference**
based on item names and common menu conventions (e.g., items prefixed "GF" or a
dedicated "Gluten Free Bun" entry). Wherever this could not be determined with
confidence, the value is marked `unknown` rather than guessed.

This is a deliberate, documented design decision, not an oversight: real dietary/
allergen data should always come from an official source or restaurant staff before
being relied on for actual health or allergy decisions. The chatbot is instructed to
disclose this uncertainty rather than assume.

## How the Chatbot Works (Rule Summary)

The system prompt defines seven core rules:

1. **Item lookup** — direct nutrition questions are answered straight from the CSV.
2. **Single dietary filter** — customers can filter by one trait at a time (vegan,
   vegetarian, or gluten-free); compound filters (e.g. "vegan AND under 500 calories")
   are intentionally not supported in this version, to keep the rule logic simple and
   predictable.
3. **Unknown flags** — if a dietary trait is `unknown` in the data, the bot says so
   explicitly and recommends asking staff, rather than guessing yes or no.
4. **Calorie/macro thresholds** — filters items above or below a stated number.
5. **Allergen questions outside the data** (nuts, dairy, shellfish, etc.) — the bot
   declines to guess and points the customer to staff or Red Robin's official allergen
   guide.
6. **Items not in the dataset** (e.g. catering or alcohol menu items) — the bot says
   it doesn't have that data rather than fabricating an answer.
7. **Comparisons** — side-by-side nutrition comparisons between two named items.

## Development Process (SDLC)

This project was built following the standard Software Development Life Cycle:

1. **Requirements & Planning** — defined chatbot scope (core menu only), output
   format (CSV + plain-text rules), and BoodleBox's expected input format.
2. **Design** — mapped PDF menu sections to data categories; decided how to handle
   missing dietary-tag data.
3. **Implementation** — built the CSV reference data and the rules-based system
   prompt.
4. **Testing** — tested in BoodleBox; caught and fixed two real bugs:
   - A comma inside a `notes` field was breaking CSV parsing (fixed by quoting the
     field, per standard CSV formatting rules).
   - The system prompt referenced gluten-free filtering, but no corresponding data
     column existed yet (fixed by adding a proper `gluten_free` column to the CSV
     and updating the relevant rules).
5. **Version Control** — all files tracked in Git and pushed to GitHub on the
   `week4-assignment` branch.

## Known Limitations & Possible Future Improvements

- Dietary flags are inferred, not officially confirmed — see limitation section above.
- The chatbot supports only single-trait filtering; compound filters (e.g. "vegan
  burgers under 400 calories") could be added in a future version.
- Only the core menu (burgers/sides/salads) is covered; catering, alcohol, and
  dessert items are out of scope for this version.

## Testing

This chatbot was manually tested in BoodleBox against a range of question types
(direct lookups, single dietary filters, unknown-flag items, out-of-scope items, and
attempted compound filters) to confirm it followed the rules defined in the system
prompt.
