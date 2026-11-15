# SBDS format draft (version 1)

SBDS (Situation Based-on Display Switching) stores user-authored text and the
conditions under which that text should be shown. It does not contain a fixed
set of application-provided display choices.

## Implementations and reuse

Anyone may create, distribute, and use software that reads or writes SBDS
files. The specification and the example file in `samples/sample.sbds` are
dedicated to the public domain under CC0 1.0. This permission covers those
materials, not the source code of any particular SBDS application. No
endorsement or trademark rights are granted. See the repository's `LICENSE`
file for details.

## File structure

An `.sbds` file is UTF-8 JSON with this shape:

```json
{
  "format": "SBDS",
  "version": 1,
  "rules": [
    {
      "id": "example",
      "name": "表示本文",
      "content": "自由に入力した本文",
      "priority": 10,
      "conditions": {
        "languages": ["ja", "az"],
        "timeRanges": [
          { "start": "09:00", "end": "14:00" },
          { "start": "18:00", "end": "23:00" }
        ],
        "dates": ["2026-11-02"],
        "dateRanges": [
          { "start": "2026-11-02", "end": "2026-11-05" }
        ]
      }
    }
  ]
}
```

Each rule has a unique `id`, a user-editable `name`, plain-text `content`,
numeric `priority`, and conditions. The editor generates IDs; users edit the
name, content, and conditions. In version 1 files created before `name` was
supported, the editor uses `表示本文` as the default name. Dates are local
calendar dates in `YYYY-MM-DD` form. `dates` lists individual dates for
backward compatibility; `dateRanges` lists inclusive ranges using `start` and
`end`. A range may cover a single day.

## Matching rules

- Values within one condition category use OR: any configured language, time
  range, individual date, or date range can satisfy that category.
- Different categories use AND. A rule with languages and time ranges must
  satisfy both categories.
- An empty condition list imposes no restriction for that category. If all
  lists are empty, the rule matches in every context.
- Language values are BCP 47 tags. A configured base tag such as `ja` matches
  a device language such as `ja-JP`. The device's preferred language list is
  considered.
- Time ranges use the device's local time, include the start, and exclude the
  end. A range may cross midnight, for example `22:00` to `02:00`.
- Dates and date ranges use the device's local calendar date. Date ranges
  include both their start and end dates.
- If multiple rules match, the rule with the highest numeric priority wins.
  When priorities tie, the rule appearing first in the file wins.
- If no rule matches, the viewer displays no content and reports that no rule
  matched. There is no implicit default rule.

## Draft limitations

Version 1 supports language tags, daily time ranges, exact dates, and date
ranges. Weekdays, time zones other than the device's local zone, and content
beyond plain text are not yet specified.