---
name: temporal-js
description: Use when writing, reviewing, or migrating JavaScript or TypeScript date/time code to the Temporal namespace, especially with time zones, calendars, immutable date objects, Safari support, tsconfig lib/target settings, or Temporal polyfills.
---

# Temporal.js

## Overview

Temporal is the standards-track date/time namespace that replaces overloaded `Date` usage with immutable value types. Current TC39 finished-proposals data lists Temporal for expected 2027 publication, so do not call it finalized ES2026 unless TC39 changes; projects can still opt in with a polyfill. Choose the object by domain meaning first, then decide serialization, formatting, and polyfill boundaries.

**Primary references:** MDN Temporal API object pages, TypeScript 6.0 Temporal types, and `temporal-polyfill`.

## Required Defaults

- Treat every Temporal object as immutable. Methods like `add`, `subtract`, `with`, `round`, and conversions return a new object.
- Prefer `import { Temporal } from 'temporal-polyfill'` for Safari and older runtimes, or `import 'temporal-polyfill/global'` only when the project deliberately wants a global polyfill.
- Use TypeScript versions that expose Temporal types. Prefer `"target": "ESNext"` and `"lib": ["ESNext", "DOM"]` / `"ESNext.Temporal"` until the installed compiler has stable ES edition libs that include Temporal.
- Do not use `Date` as an application-domain type. Use it only at legacy API, DOM, charting, or third-party library boundaries.
- Do not document or invent standalone `Temporal.Calendar` or `Temporal.TimeZone` classes. Current Temporal uses calendar IDs and time zone IDs on Temporal objects.

## Object Choice

| Domain need | Use | Serialize as |
| --- | --- | --- |
| Exact point in history, log/API timestamp | `Temporal.Instant` | `2026-05-30T12:34:56.789Z` |
| Exact point plus named zone/calendar | `Temporal.ZonedDateTime` | `2026-05-30T15:34:56.789+03:00[Europe/Moscow]` |
| Local date and time before zone is known | `Temporal.PlainDateTime` | `2026-05-30T15:30:00` |
| Calendar-only date | `Temporal.PlainDate` | `2026-05-30` |
| Clock-only time | `Temporal.PlainTime` | `15:30:00` |
| Year and month | `Temporal.PlainYearMonth` | `2026-05` |
| Month and day without year | `Temporal.PlainMonthDay` | `05-30` or `--05-30` when required by parser/interop |
| Amount of time/calendar duration | `Temporal.Duration` | `P1DT2H` |
| Current time helpers | `Temporal.Now` | Convert immediately to the domain type |

Load [references/temporal-reference.md](references/temporal-reference.md) for full object notes, MDN per-object links, construction, conversion, parsing, formatting, and migration patterns.

## Polyfill Pattern

Use the polyfill as the single import source in app code until every target runtime has native Temporal:

```ts
// src/lib/temporal.ts
export { Temporal } from 'temporal-polyfill';
```

```ts
import { Temporal } from '$lib/temporal';

const createdAt = Temporal.Instant.from('2026-05-30T12:34:56.789Z');
const dueDate = Temporal.PlainDate.from('2026-06-15');
const startsAt = Temporal.ZonedDateTime.from({
  timeZone: 'Europe/Moscow',
  year: 2026,
  month: 6,
  day: 15,
  hour: 10,
  minute: 30
});
```

Global polyfill only when the codebase already relies on `globalThis.Temporal`:

```ts
import 'temporal-polyfill/global';
```

## TypeScript Setup

For projects whose installed TypeScript exposes a stable ES edition lib containing Temporal:

```json
{
  "compilerOptions": {
    "target": "ES2026",
    "lib": ["ES2026", "DOM"],
    "module": "ESNext",
    "moduleResolution": "Bundler",
    "strict": true
  }
}
```

If that ES edition lib is not accepted by the installed TypeScript version, use `ESNext` or the granular Temporal lib:

```json
{
  "compilerOptions": {
    "target": "ESNext",
    "lib": ["ESNext", "DOM"],
    "strict": true
  }
}
```

Runtime support and type support are separate. A TS lib setting makes `Temporal` type-check; Safari and older Node/browser versions still need a polyfill.

## Core Examples

```ts
import { Temporal } from '$lib/temporal';

// Exact server timestamp.
const createdAt = Temporal.Instant.from('2026-05-30T12:34:56.789Z');

// Calendar-only birthday: never convert to midnight UTC.
const birthday = Temporal.PlainDate.from('1994-08-17');

// User-facing appointment with a real IANA time zone.
const appointment = Temporal.ZonedDateTime.from({
  timeZone: 'Europe/Moscow',
  year: 2026,
  month: 6,
  day: 12,
  hour: 10,
  minute: 30
});

// Immutable arithmetic: reassign or keep the returned value.
const tomorrowSameWallTime = appointment.add({ days: 1 });
const exactly24HoursLater = appointment.toInstant().add({ hours: 24 });

// Type-aware JSON.
const payload = {
  createdAt: createdAt.toString(),
  birthday: birthday.toString(),
  appointment: appointment.toString()
};

const restored = {
  createdAt: Temporal.Instant.from(payload.createdAt),
  birthday: Temporal.PlainDate.from(payload.birthday),
  appointment: Temporal.ZonedDateTime.from(payload.appointment)
};
```

## Review Checklist

- Did the code choose `Instant`, `ZonedDateTime`, or a `Plain*` type by domain meaning instead of convenience?
- Are returned values from Temporal methods captured?
- Are date-only values kept as `PlainDate` rather than `Date` at midnight?
- Are scheduled local events stored with an IANA time zone when future DST/calendar rules matter?
- Is `temporal-polyfill` imported for Safari/older runtimes?
- Does `tsconfig` expose Temporal types via the installed TypeScript's ESNext/granular Temporal libs or a stable ES edition lib that actually includes Temporal?
- Are `Date` and `Intl.DateTimeFormat` used only at boundaries and display layers?

## Common Mistakes

| Mistake | Fix |
| --- | --- |
| `date.add({ days: 1 });` without assignment | `date = date.add({ days: 1 })` or store a new const |
| Birthday/invoice date as `Date` | Use `Temporal.PlainDate` |
| Meeting stored only as UTC when local time intent matters | Store `ZonedDateTime` string or instant plus IANA zone |
| Adding milliseconds for calendar days | Use `ZonedDateTime.add({ days: 1 })` for wall-clock day math |
| Assuming TS lib support means runtime support | Import `temporal-polyfill` |
| Recommending `@js-temporal/polyfill` by habit | Use `temporal-polyfill` unless project policy requires another package |
| Treating `Instant` as zone-aware | Convert with `.toZonedDateTimeISO(timeZone)` for display or wall-clock fields |
