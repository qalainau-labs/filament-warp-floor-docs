# Changelog

All notable changes to `qalainau/filament-warp-floor` will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.6.3] - 2026-11-14

### Fixed

- Searching for records to place on a plan no longer fails on PostgreSQL when a type's search columns include a number or date column (`operator does not exist: integer ~~* text`).

## [1.6.2] - 2026-11-12

### Changed

- Now requires Filament 5.9 or later (was 5.0). Parts of the plugin did not work on older 5.x versions, which were never tested. The tests now also run on the lowest supported versions before each release.

## [1.6.1] - 2026-11-10

### Security

- Usage report: place names that start with `=`, `+`, `-`, `@`, a tab or a line break are written to the CSV export as plain text, so they no longer run as formulas when the file is opened in Excel or another spreadsheet app.
- Floor plan backgrounds: SVG files with scripts, embedded HTML or links written with an XML namespace prefix are now rejected like other SVGs with active content.

## [1.6.0] - 2026-11-10

### Fixed

- Editor: "Unplaced" now starts with the linked type the open floor uses most, and follows the floor you switch to.
  A floor with places but none of a linked type (an arena of seats) picks no type, so it no longer says
  "24 × Desk not on any plan yet"; the menu asks you to choose one. A type you choose yourself stays.
- Editor toolbar: every icon shows its name in a tooltip, also while it cannot be pressed (Undo, Redo, Duplicate,
  Delete, Align). The drawing tools use the same tooltip instead of the browser's delayed `title`.
- Editor: the button that leaves the editor is called "Close editor" instead of "Done", and both it and Save explain
  themselves in a tooltip (Close editor shows the plan as people see it and asks first about unsaved changes; Save
  keeps the editor open).
- Plan: a room, table or shape name that does not fit on one line ("Meeting…", "Ta…") is wrapped by words (by
  characters in Japanese, Chinese and Korean), and made up to 9px smaller if needed, before it is cut with "…". This is
  for types without `labelSize()` / `labelFit()`; those keep their own rules. A name cut down to 3 letters or fewer
  ("T…", "…") is not drawn at all; zoom in to read it.
- Plan: the capacity line of a wide area whose name is at its top ("Terrace · 24 people") is left out while the area is
  narrower than 100px on screen, so it no longer overlaps the chairs inside.
- "Your upcoming bookings" wraps between the day, the time, the place and "Check in from 13:45" on a phone, instead of
  inside them ("To/day", "14:00–/15:00").
- On a phone, the print/save button above the plan shows its name ("Print or save") next to the icon, as there is no
  tooltip on touch screens.
- Plan: the name of a tall, narrow desk (W-01 by a window) is written upright when it does not fit across the desk,
  instead of across it and over its chairs. A capacity line that does not fit a small room ("1 pers…") is left out.
- "Who's in" puts each place of a person on its own line, with a dot between the place and the time
  ("4F Focus 1 · 16:00–17:00"); "4F Focus 1 16:00" read like "Focus 116:00". The list shows a shadow at the end where
  more people are hidden, so the first eight no longer look like all of them.
- On a phone, "next week" and the calendar stay at the right end of the day strip while it scrolls, instead of being
  hidden after the last day.
- Waitlist: the Seat and Remove buttons are always on the line under the name, instead of on the same line for short
  names only. Japanese: "名簿から外す" instead of "外す". The hint of the empty panel breaks Japanese, Chinese and Korean
  text between phrases where the browser can.
- The hint of the empty side panel no longer lists "a desk, a room, a table or a bay", which named desks on a restaurant
  floor: "Click a place on the plan to see its day and book it."

### Added

- The legend above the plan explains the marks drawn on it, only when the floor has them: the circle with initials of
  who booked a place ("Booked by (initials)"), windows, plants, and filled shapes nobody books ("Drawn area (not
  bookable)", with the color of the first one). The "Available" sample is a light fill with an outline, so it looks like
  both a free desk and a free room.
- A floor without the date bar (a dashboard widget) says which moment it shows, with the floor's time zone:
  "Right now (Mon, Nov 9, 15:05 UTC)". The time is in 24 hours like the booking times (09:00–13:00); the printed
  heading of "Right now" uses the same clock instead of the browser's.
- A bookable room that is free or partly booked has a thin inner outline in the color of its state, like a desk and
  the legend sample, inside its gray wall. The marks of the legend are a row of their own, so they wrap together.
- New translation keys `filters.booked_by`, `filters.drawn`, `editor.unplaced.choose`, `editor.done_help`,
  `editor.save_help` and `time.now_at` (10 languages); `editor.done` now reads "Close editor".

## [1.5.2] - 2026-11-07

### Documentation

- README: `panelWidth('clamp(20rem, 100% - 56rem, 38rem)')` keeps the panel at `20rem` until the plan has about `56rem`,
  then lets it grow up to `38rem`; it does not give the plan `56rem` on every screen (measured at 1100, 1440 and
  1920px: at 1440px with the sidebar, a `20rem` panel and a plan of about 720px).

## [1.5.1] - 2026-11-07

### Fixed

- `releaseAfter()` without `checkIn()` did nothing, so desks were never freed and nothing said why. It now throws an
  exception that names the booking source and says to add `->checkIn('checked_in_at')`.

### Documentation

- README "Check-in": `releaseAfter()` needs `checkIn()`, no scheduled job is needed to free a desk, and a booking made
  for a time that has already started is checked in when it is saved (its `checked_in_at` is not `null` in tests).
- Laravel Boost guidelines and skill: check-in and `releaseAfter()`.

## [1.5.0] - 2026-11-06

### Added

- `Floor::panelMode(PanelMode::Overlay)` shows the side panel only while a place is selected, as a drawer over the
  right side of the plan, so the plan keeps its full width with a wide panel (`RepeatableEntry::table()` needs `36rem`).
  The plan slides left when the clicked place would be under the drawer. `PanelMode::Side` stays the default.
- `ElementType::canvasLabel(fn (FloorElement $element, ?Model $record): ?string => ...)` draws a short text on the plan
  (`07`), while the side panel, tooltip, search and screen readers keep the full name (`F-07`).
- `placesOnScreen({ decorations: true })` returns walls, doors, furniture and text in view without the places outside
  the view, and `{ offScreen: true }` the places outside the view without the decorations. `{ all: true }` is both, as
  before. Each place also has `canvasLabel`.
- While `APP_DEBUG` is true, the browser console says when the names of a type are hidden because they do not fit, and
  points to the new README section "Place names do not show".
- README: what the default tooltip shows (name, then type and state, then the bookings or the `tooltipUsing()` lines),
  and `panelWidth('clamp(20rem, 100% - 56rem, 38rem)')` to keep the plan wide.

### Changed

- `LabelFit::Shrink` draws all names of one element type in view at the same size, the size the widest one needs. When
  one of them does not fit at the `labelSize()` min, the names of that type are all hidden at that zoom and come back
  together when you zoom in. Before, each name was shrunk on its own, so narrow names such as `11` stayed while `07`
  disappeared, which looked like a bug. To get a name per place back, lower `labelSize(min: ...)`, draw a shorter
  `canvasLabel()`, or use `LabelFit::Truncate`.

## [1.4.4] - 2026-11-06

### Fixed

- Keyboard users can move a booking on a plan with the side panel. After "Move", Enter on a place in the list of
  places went back to the panel instead of moving the booking there. The focus now goes to the list after "Move" (or
  "Seat" for a waiting party), and Enter moves the booking (or seats the party) on the picked place.

## [1.4.3] - 2026-11-06

### Fixed

- Moving a booking checks `people()` against the new place's chairs or capacity, as a new booking does. A party of 6
  could be moved to a free table with 4 chairs; the move is now refused with "That one is too small for this booking."

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
