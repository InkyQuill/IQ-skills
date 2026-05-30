# Temporal Reference

## Mental Model

`Temporal` is a namespace, not a constructor. The useful classes are immutable value types: create a value, derive another value, and keep the returned result. This is intentionally different from `Date` setters.

Three questions choose the right object:

1. Is it an exact point in global time? Use `Instant`.
2. Does it need a named IANA time zone for user-facing civil time? Use `ZonedDateTime`.
3. Is it a calendar or clock value without a time zone? Use the matching `Plain*` type.

## MDN Source Map

Use these MDN pages as the canonical API references before giving detailed method guidance:

| Temporal object | MDN reference |
| --- | --- |
| `Temporal.Duration` | https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Temporal/Duration |
| `Temporal.Instant` | https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Temporal/Instant |
| `Temporal.Now` | https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Temporal/Now |
| `Temporal.PlainDate` | https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Temporal/PlainDate |
| `Temporal.PlainDateTime` | https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Temporal/PlainDateTime |
| `Temporal.PlainMonthDay` | https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Temporal/PlainMonthDay |
| `Temporal.PlainTime` | https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Temporal/PlainTime |
| `Temporal.PlainYearMonth` | https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Temporal/PlainYearMonth |
| `Temporal.ZonedDateTime` | https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Temporal/ZonedDateTime |

## Runtime And TypeScript

Install:

```bash
npm install temporal-polyfill
```

Module import:

```ts
import { Temporal } from 'temporal-polyfill';
```

Global import:

```ts
import 'temporal-polyfill/global';
```

`temporal-polyfill` includes TypeScript declarations and can polyfill Safari/older browsers. It is lightweight, supports all calendar systems and time zones, and does not require BigInt unless application code uses Temporal APIs that accept or return BigInts.

TypeScript builds may expose built-in Temporal declarations through `--target esnext`, `"lib": ["esnext"]`, or a granular Temporal lib such as `"esnext.temporal"`. Prefer an explicit stable ES edition lib only after the installed compiler actually includes Temporal in that lib.

## Object Reference

### `Temporal.Instant`

Use for exact moments independent of any time zone: logs, audit events, database timestamps, server-created values.

```ts
const instant = Temporal.Instant.from('2026-05-30T12:34:56.789Z');
const fromMs = Temporal.Instant.fromEpochMilliseconds(Date.now());
const ms = instant.epochMilliseconds;
const json = instant.toString(); // UTC/Z string
```

Convert for display or calendar fields:

```ts
const moscow = instant.toZonedDateTimeISO('Europe/Moscow');
```

Important API surface:

- Static: `from`, `fromEpochMilliseconds`, `fromEpochNanoseconds`, `compare`.
- Properties: `epochMilliseconds`, `epochNanoseconds`.
- Arithmetic: `add`, `subtract`, `since`, `until`, `round`.
- Conversion/display: `toString`, `toJSON`, `toLocaleString`, `toZonedDateTimeISO`.
- Equality: `equals`.

`Instant.from` accepts strings with a date, time, and offset. Bracketed time-zone or calendar annotations may appear in the input, but they are ignored because `Instant` stores only the exact point in time. In domain guidance, represent `Instant` as an offset/UTC timestamp; zone identity belongs to `ZonedDateTime`.

### `Temporal.ZonedDateTime`

Use for an exact instant paired with an IANA time zone and calendar. This is the main type for real-world appointments, trips, bookings, deadlines with local intent, and recurring calendar events.

```ts
const startsAt = Temporal.ZonedDateTime.from({
  timeZone: 'Europe/Berlin',
  year: 2026,
  month: 3,
  day: 28,
  hour: 9
});

const sameLocalTimeTomorrow = startsAt.add({ days: 1 });
const elapsed24Hours = startsAt.toInstant().add({ hours: 24 });
const date = startsAt.toPlainDate();
const time = startsAt.toPlainTime();
```

Use `add({ days: 1 })` when product intent is "same local time tomorrow"; use `toInstant().add({ hours: 24 })` when intent is "exactly 24 elapsed hours".

Important API surface:

- Static: `from`, `compare`.
- Identity/properties: `timeZoneId`, `calendarId`, `era`, `eraYear`, `year`, `month`, `monthCode`, `day`, `hour`, `minute`, `second`, `millisecond`, `microsecond`, `nanosecond`, `epochMilliseconds`, `epochNanoseconds`, `offset`, `offsetNanoseconds`.
- Calendar helpers: `dayOfWeek`, `dayOfYear`, `weekOfYear`, `yearOfWeek`, `daysInWeek`, `daysInMonth`, `daysInYear`, `monthsInYear`, `inLeapYear`, `hoursInDay`.
- Arithmetic/comparison: `add`, `subtract`, `since`, `until`, `round`, `equals`.
- Mutation-style derivation: `with`, `withCalendar`, `withPlainDate`, `withPlainTime`, `withTimeZone`.
- Conversion/display: `toInstant`, `toPlainDate`, `toPlainTime`, `toPlainDateTime`, `toPlainYearMonth`, `toPlainMonthDay`, `toString`, `toJSON`, `toLocaleString`.

String forms must include a bracketed time-zone annotation like `[Europe/Berlin]`; an offset alone is not enough to reconstruct a `ZonedDateTime`.

### `Temporal.PlainDateTime`

Use for date plus wall-clock time without a time zone. It does not identify a unique instant.

Good for form drafts, imported local timestamps with a separate zone field, templates, or "the store opens on this local date/time once a zone is chosen".

```ts
const local = Temporal.PlainDateTime.from('2026-05-30T10:30');
const zoned = local.toZonedDateTime('Europe/Moscow');
const date = local.toPlainDate();
const time = local.toPlainTime();
```

Do not store final scheduled events as `PlainDateTime` unless the product intentionally has no time-zone semantics.

Important API surface:

- Static: `from`, `compare`.
- Properties: `calendarId`, `era`, `eraYear`, `year`, `month`, `monthCode`, `day`, `hour`, `minute`, `second`, `millisecond`, `microsecond`, `nanosecond`.
- Calendar helpers: `dayOfWeek`, `dayOfYear`, `weekOfYear`, `yearOfWeek`, `daysInWeek`, `daysInMonth`, `daysInYear`, `monthsInYear`, `inLeapYear`.
- Arithmetic/comparison: `add`, `subtract`, `since`, `until`, `round`, `equals`.
- Derivation: `with`, `withCalendar`, `withPlainDate`, `withPlainTime`.
- Conversion/display: `toPlainDate`, `toPlainTime`, `toPlainYearMonth`, `toPlainMonthDay`, `toZonedDateTime`, `toString`, `toJSON`, `toLocaleString`.

### `Temporal.PlainDate`

Use for calendar-only values: birthdays, invoice dates, publishing dates, holidays, deadlines that are whole days.

```ts
const birthday = Temporal.PlainDate.from('1994-08-17');
const nextWeek = birthday.add({ weeks: 1 });
const dateTime = birthday.toPlainDateTime(Temporal.PlainTime.from('09:00'));
```

Never convert date-only values to `Date` at midnight unless a specific time zone has been chosen and the shift risk is intentional.

`PlainDate.from` may accept input strings that include time, offset, and time-zone annotations, but those parts are ignored. Prefer date-only strings in app code so intent stays obvious.

Important API surface:

- Static: `from`, `compare`.
- Properties: `calendarId`, `era`, `eraYear`, `year`, `month`, `monthCode`, `day`.
- Calendar helpers: `dayOfWeek`, `dayOfYear`, `weekOfYear`, `yearOfWeek`, `daysInWeek`, `daysInMonth`, `daysInYear`, `monthsInYear`, `inLeapYear`.
- Arithmetic/comparison: `add`, `subtract`, `since`, `until`, `equals`.
- Derivation: `with`, `withCalendar`.
- Conversion/display: `toPlainDateTime`, `toPlainYearMonth`, `toPlainMonthDay`, `toZonedDateTime`, `toString`, `toJSON`, `toLocaleString`.

### `Temporal.PlainTime`

Use for clock-only values: opening hours, daily reminders, schedule templates.

```ts
const opensAt = Temporal.PlainTime.from('09:00');
const closesAt = opensAt.add({ hours: 8 });
```

Combine with a `PlainDate` and time zone to create a `ZonedDateTime`.

`PlainTime.from` may accept input strings that include date, offset, time-zone, or calendar annotations, but those parts are ignored. A date-only string is rejected.

Important API surface:

- Static: `from`, `compare`.
- Properties: `hour`, `minute`, `second`, `millisecond`, `microsecond`, `nanosecond`.
- Arithmetic/comparison: `add`, `subtract`, `since`, `until`, `round`, `equals`.
- Derivation: `with`.
- Conversion/display: `toPlainDateTime`, `toString`, `toJSON`, `toLocaleString`.

### `Temporal.PlainYearMonth`

Use for a specific month in a specific year: credit card expiration, billing period, report month.

```ts
const expires = Temporal.PlainYearMonth.from('2028-04');
const nextMonth = expires.add({ months: 1 });
const firstDay = expires.toPlainDate({ day: 1 });
```

Important API surface:

- Static: `from`, `compare`.
- Properties: `calendarId`, `era`, `eraYear`, `year`, `month`, `monthCode`.
- Calendar helpers: `daysInMonth`, `daysInYear`, `monthsInYear`, `inLeapYear`.
- Arithmetic/comparison: `add`, `subtract`, `since`, `until`, `equals`.
- Derivation: `with`, `withCalendar`.
- Conversion/display: `toPlainDate`, `toString`, `toJSON`, `toLocaleString`.

### `Temporal.PlainMonthDay`

Use for recurring month/day values without a year: annual holidays, recurring birthdays when birth year is unknown or irrelevant.

```ts
const holiday = Temporal.PlainMonthDay.from('05-30');
const in2027 = holiday.toPlainDate({ year: 2027 });
```

Important API surface:

- Static: `from`.
- Properties: `calendarId`, `monthCode`, `day`.
- Equality/derivation: `equals`, `with`.
- Conversion/display: `toPlainDate`, `toString`, `toJSON`, `toLocaleString`.

`PlainMonthDay` intentionally has no `compare`, `add`, `subtract`, `since`, or `until` because ordering or arithmetic depends on the missing year and calendar context. Convert to `PlainDate` with a chosen year before doing those operations.

### `Temporal.Duration`

Use for amounts of time or calendar periods. Durations can mix calendar units and clock units.

```ts
const trial = Temporal.Duration.from({ days: 14 });
const ttl = Temporal.Duration.from('PT30M');
const later = Temporal.Now.instant().add(ttl);
```

Be explicit about whether the domain means calendar days/months or exact elapsed hours/seconds.

Important API surface:

- Static: `from`, `compare`.
- Properties: `years`, `months`, `weeks`, `days`, `hours`, `minutes`, `seconds`, `milliseconds`, `microseconds`, `nanoseconds`, `blank`, `sign`.
- Arithmetic/comparison: `add`, `subtract`, `round`, `total`, `negated`, `abs`, `with`.
- Display: `toString`, `toJSON`, `toLocaleString`.

Calendar units are context-dependent. For operations that involve years, months, weeks, or days where calendar/time-zone rules matter, pass `relativeTo` to `compare`, `round`, or `total`.

### `Temporal.Now`

Use for current-time entry points, then convert immediately to the domain type.

```ts
const now = Temporal.Now.instant();
const todayInMoscow = Temporal.Now.plainDateISO('Europe/Moscow');
const zonedNow = Temporal.Now.zonedDateTimeISO('Europe/Moscow');
```

Avoid burying `Temporal.Now.*` inside business logic that should be testable. Inject a clock or pass the current value in.

Important API surface:

- `Temporal.Now.instant()`: current exact time.
- `Temporal.Now.plainDateISO(timeZone?)`: current ISO date in a time zone.
- `Temporal.Now.plainDateTimeISO(timeZone?)`: current ISO local date/time in a time zone.
- `Temporal.Now.plainTimeISO(timeZone?)`: current ISO clock time in a time zone.
- `Temporal.Now.timeZoneId()`: current system time-zone identifier.
- `Temporal.Now.zonedDateTimeISO(timeZone?)`: current `ZonedDateTime` in a time zone.

## Parsing And Serialization

Parse with the target type:

```ts
Temporal.Instant.from(api.createdAt);
Temporal.PlainDate.from(form.birthDate);
Temporal.PlainTime.from(form.opensAt);
Temporal.PlainDateTime.from(form.localStartsAt);
Temporal.ZonedDateTime.from(api.startsAt);
Temporal.Duration.from(api.duration);
```

Serialize with `.toString()` / `.toJSON()` and keep field names type-aware:

```ts
const payload = {
  createdAtInstant: instant.toString(),
  startsAtZoned: zoned.toString(),
  birthdayDate: plainDate.toString()
};
```

`JSON.stringify` calls `toJSON`, which returns the same kind of string as `toString` for Temporal values. Still parse by field meaning on restore; JSON does not preserve the Temporal class.

## Formatting

Use `Intl.DateTimeFormat` or Temporal `toLocaleString` for presentation, not storage.

For broad compatibility with existing formatters, convert exact instants to `Date` at display boundaries:

```ts
const label = new Intl.DateTimeFormat('en-US', {
  dateStyle: 'medium',
  timeStyle: 'short',
  timeZone: 'Europe/Moscow'
}).format(new Date(instant.epochMilliseconds));
```

For date-only UI, prefer rendering fields or using `PlainDate.toLocaleString` if supported by the runtime/polyfill. Avoid `new Date(plainDate.toString())` because UTC/local interpretation can shift the displayed day.

## Forms

- `<input type="date">`: `PlainDate.toString()` and `Temporal.PlainDate.from(value)`.
- `<input type="time">`: trim seconds if the UI expects `HH:mm`; parse with `Temporal.PlainTime.from(value)`.
- `<input type="datetime-local">`: parse with `Temporal.PlainDateTime.from(value)`, then attach the user/project time zone with `.toZonedDateTime(timeZone)`.
- Time zone picker: store IANA IDs like `Europe/Moscow`, not abbreviations like `MSK` or numeric offsets unless the domain truly means fixed offset.

## Migration Patterns

From `Date` timestamp:

```ts
const instant = Temporal.Instant.from(date.toISOString());
const dateAgain = new Date(instant.epochMilliseconds);
```

From Unix milliseconds:

```ts
const instant = Temporal.Instant.fromEpochMilliseconds(ms);
```

From date-fns/Luxon-style date-only logic:

```ts
const due = Temporal.PlainDate.from('2026-06-15');
const nextDue = due.add({ months: 1 });
```

From local date/time plus zone:

```ts
const local = Temporal.PlainDateTime.from('2026-06-15T10:30');
const startsAt = local.toZonedDateTime('Europe/Moscow');
```

Comparison:

```ts
Temporal.Instant.compare(a, b);
Temporal.PlainDate.compare(a, b);
Temporal.ZonedDateTime.compare(a, b);
a.equals(b);
```

Sorting:

```ts
events.sort((a, b) => Temporal.ZonedDateTime.compare(a.startsAt, b.startsAt));
```

## Calendars And Time Zones

Most apps should use ISO calendar defaults. Non-ISO calendars are represented through calendar IDs on calendar-aware Temporal objects.

Calendar-aware types include `ZonedDateTime`, `PlainDateTime`, `PlainDate`, `PlainYearMonth`, and `PlainMonthDay`. `Instant` and `PlainTime` are not calendar-aware.

Time-zone-aware type: `ZonedDateTime`. Other types can be combined with an IANA time zone ID when converting.

## Common Review Findings

- Ignored return value from immutable methods.
- Using one type everywhere, especially `Instant` for birthdays or `PlainDateTime` for scheduled events.
- Losing IANA time zone identity by storing only UTC for future user-facing appointments.
- Parsing date-only strings with `new Date`.
- Depending on native `Temporal` in Safari without `temporal-polyfill`.
- Setting TypeScript libs for Temporal but forgetting the runtime polyfill.
- Formatting storage strings with locale formatters; storage should remain machine-readable.
