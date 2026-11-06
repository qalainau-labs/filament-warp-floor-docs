# Changelog

All notable changes to `qalainau/filament-warp-floor` will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.4.2] - 2026-11-06

### Changed

- README: a new section "Places in code (seeders and tests)" shows how to create `FloorElement`s with a full example:
  the `type`, `kind`, `shape` and `geometry` of each shape, linking records with `bookable()->associate()`, `code`,
  `label`, `props` and `category`, sections without seats, and linking the places of a template by their code.
- README: `BookingSource::canMoveTo()` is documented under "Moving and cancelling bookings", with an example that keeps
  a party at tables with enough chairs. Moves do not check `people()`; the README now says so.
- README: the usage statistics section is now "Usage report", so it no longer has the same heading (and link) as "Usage".

## [1.4.1] - 2026-11-06

### Fixed

- The callbacks of an element type (`unavailableWhen()`, `stateUsing()`, `statesUsing()`, `tooltipUsing()`,
  `modifyRecordsQueryUsing()`) got an empty new model when the record's parameter had another name than `$record`
  (`fn (Desk $desk)`), so a broken desk could be booked. The record, the range (`Window`) and the query are now passed by
  their type as well, and an argument that cannot be given throws an exception naming the arguments instead.

## [1.4.0] - 2026-11-06

### Added

- Instructions for AI coding assistants (Claude Code, Cursor, Codex) through [Laravel Boost](https://laravel.com/docs/boost):
  a guideline that Boost adds to `CLAUDE.md` and `AGENTS.md`, and the `warp-floor-development` skill for building
  floors, refusing bookings with the built-in rules and testing them. Select `qalainau/filament-warp-floor` in
  `php artisan boost:install`, or add it to `"packages"` in `boost.json`. See
  [AI coding assistants (Laravel Boost)](README.md#ai-coding-assistants-laravel-boost).

- `php artisan warp:assign-tenant "App\Models\Team" 1 [--dry-run]`: gives the plans and saved templates that have no
  tenant (made before the panel had tenancy) to one tenant, in every installed Warp plugin's tables at once. Rows of
  other tenants are not touched, and everything is changed in one transaction. See "Adding tenancy to an app that
  already has plans" in the README.
- The text of the places is in the page: each button of the hidden list after the plan has the place's name in its own
  `<span>` and `data-id`, `data-type`, `data-code`, `data-label`, `data-record` and `data-state` (the same values as
  `placesOnScreen()`). Playwright's `getByText()`, `innerText` and screen readers find the places drawn on the canvas.
  In the editor the list follows unsaved changes (placed, renamed and deleted places).
- `ElementType::statesUsing(fn (Collection $records, Collection $bookings, Window $window): array)`: works out the
  states of all the places of a type on the plan in one call (record key => state name), for states that come from a
  total such as stock against a reorder point. It wins over `stateUsing()`; records it leaves out or returns `null` for
  are decided by `stateUsing()`, `unavailableWhen()` and the bookings, as before. It runs again on every refresh, so no
  old state stays. See "Working out the states of all places at once" in the README.
- Plans can be linked to one of your records: `FloorPlan::owner()` (a morph relation on new `owner_type` and
  `owner_id` columns), so `$floor->plans(FloorPlan::query()->whereMorphedTo('owner', $location))` finds a plan even
  after it is renamed. A plan added from an open plan, and a floor copied on the venues page, get the same owner.
  **Run `php artisan migrate`** after updating. See "Linking a plan to one of your records" in the README.
- README: "Testing with tenancy" (another team's IDs are treated as not there, not as an error; how to check what a
  team sees) and "The text of the places in the page".

### Changed

- `Floor::panelWidth()` now wins over the limit of half the floor's width: the panel gets the width you set and the
  plan becomes narrower. When the plan would be narrower than 20rem beside it, the panel moves below the plan at full
  width. Without `panelWidth()` nothing changes.

## [1.3.1] - 2026-11-02

### Fixed

- Translations read more naturally. Japanese calls a floor of a building 「フロア」 everywhere (it mixed 「階」 and
  「フロア」); French, Spanish, Brazilian Portuguese and Chinese name "Let us pick the best seats" the way ticket sites
  do; Korean and Taiwanese Chinese use the words offices use for a desk island; Spanish speaks to the user with tú
  everywhere (it mixed usted and tú), like the demos; and a few German and French terms are now the same on every
  screen.

## [1.3.0] - 2026-10-31

### Added

- `Floor::labelSize(min, max)` and `ElementType::labelSize(min, max)`: the size of place names in screen pixels, for
  the whole floor or per type. `max` lets names grow beyond the default 12–13px as far as the place allows; `min` keeps
  them readable when zoomed out (a name is hidden only when the place is lower than `min`), so codes like `A-05-3` on a
  warehouse map can be read on a full-HD screen.
- `Floor::labelFit()` and `ElementType::labelFit()` with the new `LabelFit` enum: `LabelFit::Shrink` makes a name that
  is too wide smaller until all of it fits, instead of cutting it with "…" (`LabelFit::Truncate`, the default).
- `placesOnScreen()` on the floor's Alpine component: where each place is on the screen (center and box in viewport
  pixels, with its type, code, name, record and state), for browser tests, guided tours and screen recordings.

Floors that do not call `labelSize()` or `labelFit()` look exactly as before.

## [1.2.0] - 2026-10-31

### Changed

- The day strip (and the time picker of `hourly()` floors) is decided per plan: a plan without places of a type with
  bookings — a warehouse next to your office floors — no longer shows it. `->timePicker()` shows it on every plan as
  before, and `->timePicker(false)` still hides it everywhere.
- On plans without bookings, "How to use" explains how to find a place and see its details instead of how to book, and
  the side panel no longer asks people to click a place to book it.

### Removed

- The unused `panel.more` translation (the side panel shows record actions as buttons since 1.1.0).

## [1.1.0] - 2026-10-31

### Added

- `ElementType::panelSchema()`: show your own infolist (text, tables of related records, sections) in the side panel
  when a place is clicked, for types with or without bookings — for example what is on a warehouse rack.
  `ElementType::panelView()` renders a Blade view instead. The content is rendered again after bookings, record actions
  and polling, and never shows a previously selected place.
- `Floor::panelWidth()`: a wider side panel, for example to show `RepeatableEntry::table()` as a table.

### Changed

- The side panel lists the type's `recordActions()` as buttons instead of behind "More", so an action is one click
  away.

### Fixed

- The side panel no longer shows an older answer when the same place is read again twice in quick succession.

## [1.0.1] - 2026-10-27

### Changed

- A new banner image (`art/banner.jpg`): the editor and the booking basket from the demo. No code changes.

## [1.0.0] - 2026-10-27

### Added

- First release: floor plans and seat maps on a canvas for Filament 5, read from your own Eloquent models and booking
  tables. `FloorPage`, `FloorWidget`, a venues page with a card per venue and "New venue", and a usage page.
- Kinds of places drawn like a real plan: seats, sections, desks with chairs, rooms, tables with chairs, standing areas,
  parking bays, walls, doors, windows, furniture, text and shapes. Templates for offices, theaters, arenas,
  restaurants, car parks and blank plans.
- Workplaces: a side panel with features, who booked a place and its day; initials or photos of the people who booked;
  "Yours" for the signed-in user; check-in with automatic release; favorites; filters for features and the number of
  people; people search; repeat bookings, also on chosen days of the week; "Your upcoming bookings" for the next two
  weeks; `whoIsIn()`; team areas (`ElementType::neighborhood()`, `Floor::myNeighborhood()`) with an "Area" filter.
- Floors booked by the day (a strip of days, "Book for tomorrow") or by the hour (`hourly()`, within business hours).
  Click a free time on a place's schedule bar to book from then. `bookingLength()`, `onePerPerson()` and `people()`.
- Seat maps: sections that lay out their seats from rows, seats, spacing, curve and rotation, with row letters and seat
  numbers; shows instead of time ranges; price categories with seats left; a basket with "Best seats" ("Pick your seats
  on the map" or "Let us pick the best seats"); standing areas sold by headcount; `basketLimit()`.
- Restaurants and front desks: `walkIns()` ("Seat now"), `waitlist()` with `Waitlist`, and `manageBookings()` to move
  or cancel bookings from the side panel; `BookingSource::canMoveTo()` decides where a booking may move.
- Confirmations by email with an `.ics` file (`BookingSource::confirmations()`), `Floor::addToCalendar()`,
  `releaseNotices()` with the `warp-floor:release-notices` command, and the `BookingCreated`, `BookingMoved`,
  `BookingCancelled` and `BookingReleased` events.
- `checkInCodes()`: print QR codes for the desks; scanning one checks in to your booking there. `?element=` opens a page
  on a place.
- An editor with a "What to place" panel that starts with the records still to place, sliders for sections, walls with
  their length, doors and windows set into walls, alignment guides, price categories, shapes, rows and columns, undo,
  versioned saving, and saved templates of a floor or a selection. `openInEditor()` and `VenuesPage::editPageFor()`.
- A usage page with a heat map, busy hours, no-shows, use by kind and a CSV download.
- Bookings are checked in one transaction that holds the places, so two people cannot book the same seat or desk at
  the same moment.
- Element types linked to your models, booking states read from your own booking tables, record actions, tooltips,
  polling, PNG / JPG / GIF / WebP / SVG / PDF backgrounds, unplaced records linked by drag and drop, policies, search,
  printing and PNG export, an overview map, keyboard and screen reader support, touch gestures, dark mode, a guide for
  the people who use the floor (`help(false)` turns it off), and translations in English, Japanese, German, French,
  Spanish, Italian, Brazilian Portuguese, Korean, and simplified and traditional Chinese.
- Filament's multi-tenancy: in a panel with `->tenant(Team::class)`, places and bookings are scoped to the current team
  (`scopeToTenant()`, `tenantOwnershipRelationshipName()`, `VenuesPage::$isScopedToTenant`). Tenants and users with
  UUID or ULID keys are supported.
