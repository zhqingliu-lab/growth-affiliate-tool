# `periodType` quick reference

The `periodType` parameter is shared across `affiliate-pro-performance-summary`,
`affiliate-pro-invitee-list`, and `affiliate-pro-sub-affiliate-list`.

| Value | Meaning                                                       |
| :---: | ------------------------------------------------------------- |
| `0`   | Last 7 days                                                   |
| `1`   | Last 30 days                                                  |
| `2`   | This month (calendar-month-to-date, UTC)                      |
| `3`   | Last calendar month                                            |
| `4`   | Custom — must be paired with `periodStart` + `periodEnd`      |
| `5`   | All-time (default)                                             |
| `6`   | Today (current UTC day)                                        |
| `7`   | This week (current UTC week)                                  |

## Custom range example

```json
{
  "periodType": 4,
  "periodStart": "2026-04-01",
  "periodEnd":   "2026-04-30"
}
```

Both `periodStart` and `periodEnd` are inclusive `YYYY-MM-DD` dates and are interpreted in
**UTC**.

## Notes

- All time-based aggregates use UTC day boundaries. `Today` = `2026-05-07 00:00:00 UTC` →
  `2026-05-07 23:59:59 UTC`.
- The endpoint exposes data starting from the date your affiliate account was first
  activated. Custom ranges that begin before that date are silently clamped to the activation
  date.
- For trends across many days, prefer many small custom ranges over one big one — it lets you
  spot daily anomalies without losing precision.
