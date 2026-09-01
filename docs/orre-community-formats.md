# Orre community formats — canon reference

Settled by the community organizers on 2026-08-27 (Discord, relayed by Logan). This SUPERSEDES
the 2023 Smogon-post format list: their words on that list were "definitely
dont use that" / "its also incomplete" — do not implement from it.

## The six formats to build (matches their Showdown side server)

The structure: pick one of three RULESETS, times two ENTRY SHAPES.

Rulesets ("same as Orre but ..."):

- **Standard** (today's Orre Colosseum rules): Restricted ("Box" legendaries)
  and Mythical Pokemon banned, Soul Dew banned, Species Clause, Item Clause;
  Sleep/Freeze/Self-KO are honor clauses. Level 100.
- **Unlimited**: same as Orre but ALL Pokemon and items allowed (Soul Dew
  legal, Mythicals legal). Level 100.
- **Limited**: same as Orre but LEVEL 50 and legendaries ALL banned (the
  Restricted/Mythical bans PLUS every other legendary).

Entry shapes:

- **Orre** = bring 6, pick 4, DOUBLE battles.
- **Hoenn** = bring 6, pick 3, SINGLE battles (corrected by Logan 2026-08-30;
  the first implementation wrongly assumed doubles).

Clauses (Logan, 2026-08-30): Item Clause, Sleep Clause, Freeze Clause and
Self-KO Clause (Explosion/Destiny Bond/Perish Song with your final Pokemon
loses you the game) apply to EVERY format, Orre and Hoenn alike. DECODED
2026-08-30: the stock tournament preset bytes OrreLink pins carry all of
these clauses ON (ruleset +0x0C..+0x13; note +0x11's inverted polarity), so
the game itself enforces them -- they are NOT honor rules.

Giving the six selectable formats:

1. Orre Colosseum      (Standard, 6 pick 4) — already shipped
2. Orre Unlimited      (Unlimited, 6 pick 4)
3. Orre Limited        (Limited @ Lv50, 6 pick 4)
4. Hoenn Stadium       (Standard, 6 pick 3)
5. Hoenn Unlimited     (Unlimited, 6 pick 3)
6. Hoenn Limited       (Limited @ Lv50, 6 pick 3)

"You just pick one of those three and then a team for whatever format you
want to play." — the Showdown server models it the same way.

## Explicitly deferred (settled rules, but NOT in scope now)

- **Realgam Colosseum** — like Orre but up to two uber-legendaries per team.
- **Phenac Stadium** — Little Cup; has a Smogon post with its rules.

Everything else from the old subformat list (Neo, Classic, Under, Pyrite,
and the old Unlimited/Limited/Realgam writeups) is unsettled or superseded —
do not build from it.

## Implementation notes (OrreLink side)

- Validation layer (FormatRules): Unlimited validates nothing; Limited =
  Standard's ban list plus all remaining legendaries (Articuno, Zapdos,
  Moltres, Raikou, Entei, Suicune, Regirock, Regice, Registeel, Latias,
  Latios — confirm the exact list with the community before shipping); Hoenn variants
  share their ruleset's validation unchanged.
- Rules-pin layer (BattleCustomizer): the matrix is {Lv100, Lv50} x
  {entries 4, entries 3}. Lv100/entries-4 is the shipped pin (stock slot 3 of
  the DOL preset table at file 0x2E7C08 + slot*0x90, entries byte patched).
  The Lv50 stock tournament preset is DOL slot 2 (min=1 max=50 total=300,
  same +7=06 structure as slot 3); entries 3 is the same byte patched to 3.
- Level is enforced by the game's own rules screen (the pin), not by paste
  validation.
- ENTRY MODE (decoded 2026-08-30): ruleset +0x1C u16 must be 0 or the game
  ignores the entries count (+0x1A) and plays all 6 -- the getter at
  0x8004cfe0 returns 6 whenever +0x1C != 0. The stock preset ships +0x1C=1;
  OrreLink zeroes it, which makes the game's own pick-N flow run (full 4v4/
  3v3 enforcement chain verified by disassembly).
- SOUL DEW cannot be banned in-game for custom rule slots: the 32-entry
  bannable-item table (0x8032EB08) does not contain item 191, and the game's
  hardcoded Soul Dew check lives only in the default-rules builder that
  custom slots bypass. App-side gates (paste/host/submission) are the Soul
  Dew enforcement, by structural necessity.
