# Task Plan

One list per person. Each task says what you need before you start (Inputs),
what you hand off when you're done (Outputs), and how we check it works. When
one task's output is another task's input, it uses the same name in both places,
so you always know who you're waiting on.

The diagram of who waits on whom is in [dependencies.md](dependencies.md). Track
your status (done, blocked, next) in the shared task sheet before each check-in.

## Who does what

| Person | Your job in a sentence |
| ------ | ---------------------- |
| **Devran** | Set up the project, build the scraper, search page, listing page, and location score, and review everyone's code. |
| **Sean** | Build everything behind a login: the database, JMU sign-in, saved listings, alerts, roommate matching, and sublease. |
| **Nathan** | Make the site look good and work on phones, and build the price chart and the About / FAQ page. |
| **Ethan** | Collect past rents, decide what counts as a "fair price," and check our prices against the real sites each week. |
| **Runa** | Find out what students want (survey), sketch the screens, write the roommate questions, and make the demo slides. |
| **Will** | List the 10 apartment complexes and their floor plans, write the test checklist, and test the whole site in Week 5. |

## Demos and deadlines

| Milestone | When | Needs |
| --------- | ---- | ----- |
| Week 2 board demo | End of Week 2 (Nov 6) | D3 price scraper running, D5 search page, S3 login, R4 slides |
| Week 4 demo + feature freeze | End of Week 4 (Nov 20) | S4 saved listings, S5 price alerts, S7 roommate matching, S8 sublease, R4 slides. No new features after this, only fixes. |
| Final board demo | End of Week 5 (Dec 4) | W4 testing done, N4 mobile pass, R4 slides |

---

## Devran (project lead)

| Task | Depends on | Inputs | Outputs | How to test |
| ---- | ---------- | ------ | ------- | ----------- |
| **D1 Project setup** (Wk 1, Mon) | — | Proposal | GitHub repo with Pages on; Supabase project (URL and public key in the repo config); shared task sheet; Drive folder | Everyone can open the repo, sheet, and Drive; the Pages URL loads a placeholder page |
| **D2 Data contract** (Wk 1, Tue) | D1 | Proposal Sections 3 and 7 | **DATA.md**: every table and column, ID format (complex_id, listing_id), price unit (per person per month). 3 empty Drive templates (**Complexes**, **Listing Details**, **Rent History**) whose columns match DATA.md exactly | Sean and Ethan read it and confirm in chat; every template column is in DATA.md |
| **D3 Daily price scraper + Actions job** (Wk 2) | S2 | complexes table (pricing_url); listings table (floor_plan_name); each site's robots.txt | One **price_snapshots** row per listing per day; listings.current_price and last_updated filled in; a log of sites that failed, for manual entry | Run it by hand and today's rows appear. The next day, new rows appear with no manual work. Blocked sites show in the log |
| **D4 Location scoring** (Wk 3) | S2 | complexes table (lat/lng); OpenStreetMap groceries and bus stops; routing API; bus schedule | **Score columns** on the complexes table (walk min, bus min, grocery min, nearby stops, total); **score formula in the README**; D5's distance filter switched from straight-line miles to walk minutes | Work out one complex by hand from the README and it matches the stored score |
| **D5 Landing page + search & filters** (Wk 2) | S2, R2, N1 | listings and complexes tables; wireframes; styles.css | Landing page with filters (price, distance, group size, furnished, pets, elevator, ground floor), live result count, and **result cards** that link to the listing page by listing_id. Distance is straight-line from the complex's lat/lng until D4 is done | Every filter case in the test checklist passes; the count changes as each filter changes |
| **D6 Listing page** (Wk 3) | D5, D3, D4, N3 | listing_id from a result card; current_price and last_updated; score columns; price chart component | **Listing page**: price, source link, last updated, score breakdown, map, and chart, plus an empty **Save button slot** for S4 | Open 3 listings and check each value against the database |
| **D7 PR review + check-ins** (weekly) | — | Open PRs; task sheet | Merged PRs; check-in notes; **demo plan** (date and which features to show) for each demo | No PR stays open longer than 2 days; task sheet updated before each check-in |
| **D8 Commute simulator** (stretch, Wk 5) | D4, D6 | Score columns and routing setup; listing page | Commute section on the listing page: enter a class schedule, see daily walk and bus time | Enter a known schedule and compare with a hand estimate |

Only start D8 if everything for the Week 4 demo is done.

## Sean (backend)

| Task | Depends on | Inputs | Outputs | How to test |
| ---- | ---------- | ------ | ------- | ----------- |
| **S1 Database tables + access rules** (Wk 1) | D2 | DATA.md | All tables from DATA.md (complexes, listings, price_snapshots, rent_history, profiles, saved_listings, alerts, roommate_answers, sublets, reports) with read and write rules | Add and read a test row in each table. A logged-out user can read listings but not roommate_answers |
| **S2 Sheet import** (Wk 1 end, rerun when sheets change) | S1, W1, W2, E1 | Complexes, Listing Details, and Rent History sheets | Filled **complexes, listings, and rent_history tables** | Row counts match the sheets; spot-check 5 rows per table |
| **S3 JMU-email login** (Wk 2) | S1 | Supabase auth | Sign up, log in, and log out pages; a shared **auth helper** other pages use to get the current user | A JMU email gets in; a Gmail address is rejected |
| **S9 Scraper review** (Wk 2) | D3 | Scraper code and job | Review comments on the PR; Sean can run the job alone | Sean runs the job by hand without help |
| **S6 Roommate questionnaire** (Wk 3) | S3, R3 | Roommate questions doc; auth helper | Questionnaire page; one **roommate_answers** row per user | Submit as a test user; the saved row matches the answers |
| **S4 Saved listings** (Wk 4) | S3, D6 | Auth helper; Save button slot on the listing page | **saved_listings** rows; "My saved" page | Save a listing on a laptop, then see it on a phone |
| **S5 Price alerts** (Wk 4) | S4, D3 | saved_listings; daily price_snapshots | alerts rows; a "price dropped" notice on the My saved page after each daily run. On-site notice only, no email | Set an alert, lower a test price by hand, rerun the job, and the notice shows up |
| **S7 Roommate matching** (Wk 4) | S6 | roommate_answers rows | Ranked matches page, only for logged-in JMU users | Two test users with similar answers rank each other near the top; a logged-out user sees nothing |
| **S8 Sublease marketplace** (Wk 4) | S3, N1 | Auth helper; styles.css | **Sublease pages** (post and browse); sublets and reports rows; landlord-approval notice; report button | A post by user A shows up for user B; a non-JMU email can't post; Report creates a row |

If we fall behind, cut in this order: D8 stretch, then S5 price alerts, then N3 price history.

## Nathan (developer)

| Task | Depends on | Inputs | Outputs | How to test |
| ---- | ---------- | ------ | ------- | ----------- |
| **N1 Site-wide styling** (Wk 1-2) | D1, R2 | GitHub repo; wireframes; JMU-inspired purple and gold, no official logos | **styles.css** (colors, fonts, buttons, cards); shared footer; short style guide in the README | Every page uses it; text passes a contrast checker |
| **N3 Price history chart + fair-price badge** (Wk 3) | D3, S2, E2, N1 | price_snapshots; rent_history table; fair-price rule doc; styles.css | A **price chart component** that takes a listing_id and shows the trend and a fair-price badge | The badge on 3 listings matches Ethan's worked examples |
| **N2 About / FAQ page** (Wk 3) | N1, D4, E2, R1 | styles.css; score formula in the README; fair-price rule doc; survey summary | About / FAQ page explaining the score, fair price, data sources, and sublease rules | Each FAQ answer matches the README and the fair-price rule |
| **N4 Mobile + accessibility pass** (Wk 5) | D5, D6, S8 | Landing page; listing page; sublease pages | Fixes merged | Every page works at phone width; you can use the whole site with only a keyboard |

The proposal lists Pricing data (Ethan) as lead for price history. Ethan
decides the fair-price rule (E2); Nathan builds the chart (N3).

## Ethan (pricing data)

| Task | Depends on | Inputs | Outputs | How to test |
| ---- | ---------- | ------ | ------- | ----------- |
| **E1 Past rents** (Wk 1-2) | D2, W1 | Rent History template; complex_id list from the Complexes sheet | Filled **Rent History sheet**: complex, bedrooms, year, rent, source link | No blank required cells; every complex_id matches the Complexes sheet; every row has a source |
| **E2 Fair-price rule** (Wk 2) | E1 | Rent History sheet | **Fair-price rule doc**: how a price compares to the average for the same unit type, the cutoff for each badge, and 3 worked examples | Nathan works through the 3 examples by hand and gets the same badges |
| **E3 Weekly price spot-check** (Wk 2-5) | D3, W3 | Listing prices on our site; complex websites; bug log tab | Spot-check tab in the task sheet (date, listing, site price, our price, match); mismatches added to the **bug log** | 10 checks logged each week |

## Runa (product & UX)

| Task | Depends on | Inputs | Outputs | How to test |
| ---- | ---------- | ------ | ------- | ----------- |
| **R1 Student survey** (Wk 1, by Wed) | — | Proposal feature list | **Survey summary** in Drive: top 5 needs, most-wanted filters, roommate concerns | At least 20 responses; summary posted by Wednesday |
| **R2 Wireframes** (Wk 1, by Fri) | R1 | Survey summary; 2-3 existing housing sites to learn from | **Wireframes** in Figma: landing page and filters, results, listing page, questionnaire, sublease | Devran signs off; every Section 3 feature has a screen |
| **R3 Roommate questions** (Wk 2) | R1 | Survey summary; proposal topics (cleanliness, sleep, social, budget) | **Roommate questions doc**: 8-10 questions, each with fixed answer choices | Sean confirms each answer can be stored as one choice |
| **R4 Demo slides** (Wk 2, 4, 5) | D7 | Demo plan from the check-ins; working features | **Slide deck** for each board demo | Ready a day before each demo; every slide matches a working feature |

## Will (data & QA)

| Task | Depends on | Inputs | Outputs | How to test |
| ---- | ---------- | ------ | ------- | ----------- |
| **W1 Complex list** (Wk 1, by Wed) | D2 | Complexes template | Filled **Complexes sheet**: 10 complexes with complex_id, name, address, lat/lng, website, and pricing_url (the page that lists prices) | Every link opens; each lat/lng drops a pin on the right building; Devran approves the 10 |
| **W2 Listing details** (Wk 1, by Fri) | W1, D2 | Complexes sheet; Listing Details template | Filled **Listing Details sheet**: one row per floor plan with listing_id, complex_id, floor_plan_name (exactly as on the pricing page), bedrooms, baths, furnished, pets, elevator, ground floor, source link | No blank cells; every complex_id matches the Complexes sheet |
| **W3 Test checklist + bug log** (Wk 2) | R2 | Proposal Section 7; wireframes | **Test checklist** (steps and expected result per feature); empty **bug log** tab in the task sheet | Every Section 7 row has at least 2 test steps; the bug log tab exists for E3 |
| **W4 Final testing** (Wk 5) | W3, Week 4 demo + feature freeze | Test checklist; site frozen after Week 4 | Bug log filled in: pass or fail for each step | Every checklist step is marked pass or fail |

W1 and W2 are on the critical path: the Week 2 demo can't happen without them.
Devran checks in with Will on Wednesday, Oct 28. If W1 isn't done by then, or W2
isn't half done by Thursday, Devran and Sean finish it so the import isn't held up.

---

## Changes from the first draft

- **Prices are per complex.** Each complex has one pricing page link (W1).
  The scraper (D3) matches floor plans on that page by floor_plan_name (W2).
- **The distance filter works in Week 2.** It uses straight-line distance until
  location scoring (D4) is done in Week 3, then switches to walk minutes.
- **The bug log starts in Week 2.** W3 creates it so Ethan's spot-checks (E3)
  have somewhere to go.
- **Price alerts show on the site, not by email.** Email would need extra
  Supabase setup.
- **There's a backup plan for Will's sheets** (see Will's section).
- **There are now 3 demos:** the Week 4 demo is combined with the feature
  freeze, and the final demo is in Week 5. Runa's slides (R4) feed all three.
