# PRD Appendix C-F (Implementation Ready)

## C. Excel/XLSX dependency (fixed)
Use poi-android through JitPack:

```kotlin
dependencyResolutionManagement {
  repositories {
    google()
    mavenCentral()
    maven("https://jitpack.io")
  }
}

dependencies {
  implementation("com.github.SUPERCILEX.poi-android:poi:3.17")
  implementation("com.github.SUPERCILEX.poi-android:proguard:3.17")
}
```

## D. DAO SQL (API26-safe, no window functions)

### D.1 My aggregate value (task result)
```sql
SELECT value
FROM metric_values
WHERE sessionId = :sessionId
  AND taskId = :taskId
  AND trialIndex = 0
  AND metricKey = :metricKey
LIMIT 1;
```

### D.2 Distribution values (exclude self + latest session per participant)
```sql
SELECT mv.value
FROM metric_values mv
JOIN sessions s ON s.sessionId = mv.sessionId
WHERE mv.taskId = :taskId
  AND mv.trialIndex = 0
  AND mv.metricKey = :metricKey
  AND mv.value IS NOT NULL
  AND s.participantId != :selfParticipantId
  AND s.sessionId = (
      SELECT s2.sessionId
      FROM sessions s2
      WHERE s2.participantId = s.participantId
      ORDER BY s2.createdAtMs DESC, s2.sessionId DESC
      LIMIT 1
  )
ORDER BY mv.value ASC;
```

### D.3 Distribution count
```sql
SELECT COUNT(1)
FROM metric_values mv
JOIN sessions s ON s.sessionId = mv.sessionId
WHERE mv.taskId = :taskId
  AND mv.trialIndex = 0
  AND mv.metricKey = :metricKey
  AND mv.value IS NOT NULL
  AND s.participantId != :selfParticipantId
  AND s.sessionId = (
      SELECT s2.sessionId
      FROM sessions s2
      WHERE s2.participantId = s.participantId
      ORDER BY s2.createdAtMs DESC, s2.sessionId DESC
      LIMIT 1
  );
```

### D.4 Trend / first session
```sql
SELECT sessionId
FROM sessions
WHERE participantId = :participantId
ORDER BY createdAtMs ASC, sessionId ASC
LIMIT 1;
```

```sql
SELECT s.sessionId AS sessionId,
       s.createdAtMs AS createdAtMs,
       mv.value AS value
FROM sessions s
LEFT JOIN metric_values mv
  ON mv.sessionId = s.sessionId
 AND mv.taskId = :taskId
 AND mv.trialIndex = 0
 AND mv.metricKey = :metricKey
WHERE s.participantId = :participantId
ORDER BY s.createdAtMs ASC, s.sessionId ASC;
```

## E. Export skeleton
- Generate all of:
  - `metrics_long.csv`
  - `summary.csv`
  - `summary.xlsx` (3 sheets: Sessions / TaskAggregates / MetricsLong)
- Include UTF-8 BOM for CSV.
- If XLSX fails, CSV generation must still succeed.

## F. Zip skeleton
Zip from temp export dir into:
`PDHL_export_{participantCode}_{yyyyMMdd}_{sessionId}.zip`

Must include DB copy, metrics files, session artifacts, and asset copies.
