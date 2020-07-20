# The date API in Java 7
* create a specific data
```
Calender cal= Calender.getInstance();
cal.set(2018,5,10);
Date date =cal.getTime()
```
* add more days
```
cal.add(Calender.DAY_OF_MONTH, 7);
Date oneWeekLater = cal.getTime();
```
* The Date class is mutable, object of Date can be modified outside
  * use a defensive copy to prevent it: create new date object which different from object variable.
  
# The Date API in Java 8

* New API, package is `java.time`
* Interoperatio with the legacy API: `.from()`
* new API to Formate a Date: `DateTimeFormatter`

## New Concepts
### 1. Instant
* a point on the time line which precision is the nanosecond.
* instant.MIN, instant.MAX,Instant.now()
* An instant is immutable
* Day percision
## 2. Duration
* `Duration`: time elapsed between two different Instants.
* Methods
  1. toNanos().
  2. toMillis()
  3. toSeconds()
  4. toMinutes()
  5. toHours()
  6. toDays()
  7. minusNanos()
  8. plusNanos()
```
Instant start= Instant.now();
Instant end= Instant.now();
Duration elapsed =Duration.between(start,end);
long millis = elapsed.toMillis();
```
### 3. LocalDate
* Day precision
```
localDate now = LocalDate.now();
LocalDate dateOfBirth= LocalDate.of(1992,Month.AUGUEST,19);
```
### 4. `Period`
* the amount of time between two `LocalDate`
```
Period p=dataOfBirth.until(now);
System.out.println(“# Years =” + p.getYeas());

long days= dataOfBirth.until(now, ChronoUnit.DAYS);
Syste.out.println("# days = "+ days);
```

### 5. `DataAdjuster`
* Add(or subtract) an amount of time to an Instant or LocalDate
* Methods
  1. firstDayOfMomth(),lastDayOfMonth();
  2. firstDayOfYear(),lastDayOfYear();
  3. firstDayofNextMonth(),firstDayOfNextYear();
```
LocalDate now= LocalDate.now();
LocalDate nextSun= now.with(TemporalAdjusters.next(DayofWeek.SUNDAY));
```

### 6. `LocalTime`

* a time of day
```
LocalTime now= LocalTime.now();
LocalTime time= LocalTime.of(10,20);
```
* Plus a set of methods to manipulate

### 7. Zoned Time
* `ZoneId` class
* a set o methods to manipulate zoned time

## Formate a Date
* new API: `DateTimeFormatter`
