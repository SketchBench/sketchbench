# ESPBenchmark

> - Website: [https://hpi.de/plattner/projects/espbench.html](https://hpi.de/plattner/projects/espbench.html)
> - Repo: [https://github.com/guenter-hesse/ESPBench](https://github.com/guenter-hesse/ESPBench)
> - Query sources
>   - [ESPBench repo](https://github.com/guenter-hesse/ESPBench/blob/master/docs/submitted_paper.pdf)

## Queries

### Query 1: Check Sensors

> Calculate avg, min, max, count for the last 1sec for mf01 for monitoring.

**CQL**:

```sql
SELECT 
  AVG(mf01), 
  MIN(mf01), 
  MAX(mf01), 
  COUNT(mf01) 
FROM 
  STREAM_SENSOR TUMBLING WINDOW 1 SECONDS;
```

**BQL**:

```sql
SELECT 
  AVG(mf01), 
  MIN(mf01), 
  MAX(mf01), 
  COUNT(*) 
FROM 
  STREAM() WINDOWING TUMBLING(1000, TIME);
```

### Query 2: Determine Outliers

_Skipped since it uses custom function._

### Query 3: Identify Errors

> Log if sensor value electrical power main phase 1 exceeds limit of 14,963

**CQL**:

```sql
SELECT 
  * 
FROM 
  STREAM_SENSOR 
WHERE 
  mf01 > 14963;
```

**BQL**:

```sql
SELECT 
  * 
FROM 
  STREAM() 
WHERE 
  mf01 > 14963;
```

### Query 4: Check Machine Power

_Skipped since it uses joins._

### Query 5: Persist Processing Times for Products

_Skipped since it uses external DBMS._
