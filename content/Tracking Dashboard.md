```dataview
TABLE 
    choice(session_long, "✅", "❌") AS "Long Session",
    choice(session_medium, "✅", "❌") AS "Medium Session",
    choice(session_short, "✅", "❌") AS "Short Session",
    choice(islamic_hour, "✅", "❌") AS "Islamic Hour"
FROM "08-Daily-Notes"
WHERE file.cday > date(today) - dur(7 days)
SORT file.name desc
```

```tracker
searchType: frontmatter
searchTarget: session_long, session_medium, session_short, islamic_hour
folder: 08-Daily-Notes
month:
    mode: sum
    color: green
    headerMonthColor: white
    dimNotInMonth: false
```

```tracker
searchType: frontmatter
searchTarget: session_long, session_medium, session_short, islamic_hour
folder: 08-Daily-Notes
summary:
    template: "Average Daily Completed Sessions: {{sum()}}"
line:
    title: "Daily Execution Rate"
    yAxisLabel: "Completed Sessions"
    yMin: 0
    yMax: 4
    lineColor: cyan
    showPoint: true
```
