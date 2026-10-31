# Changelog

All notable changes to `qalainau/filament-warp-floor` will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

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
