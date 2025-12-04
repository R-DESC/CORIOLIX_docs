# How to Parse a VBW Message Using Regular Expressions
## 1. Example VBW Sentence

We'll start with a real VBW message:

```
$VDVBW,8.2,0.5,A,8.1,0.4,A,8.3,A,8.2,A*57
```

VBW is an NMEA-style sentence consisting of:

- A start character ($)
- A sentence identifier (VDVBW)
- A sequence of comma-separated fields
- A * followed by a two-digit hexadecimal checksum

Every field contains either a value or may be blank.

## 2. Understanding the Fields

A VBW message contains 10 comma-separated data fields after the sentence ID:

| Field | Meaning (simplified)                   |
| ----- | -------------------------------------- |
| 1     | Longitudinal water speed               |
| 2     | Transverse water speed                 |
| 3     | Status (water speed)                   |
| 4     | Longitudinal ground speed              |
| 5     | Transverse ground speed                |
| 6     | Status (ground speed)                  |
| 7     | Transverse water speed (stern sensor)  |
| 8     | Status (stern water speed)             |
| 9     | Transverse ground speed (stern sensor) |
| 10    | Status (stern ground speed)            |

A field may be present:
```
8.2
```
or blank:
```
,
```
So our regex needs to allow for both situations.

## 3. Start Building the Regex
### Step 1 – Match the sentence start
```
^\$VDVBW,
```

- `^` = start of the line
- `\$` = literal $
- `VDVBW,` = the sentence identifier and the first comma

### Step 2 – Capturing Fields

Since any field may be blank, the easiest and most robust pattern is:
```
[^,]*
```
Meaning:

"Match zero or more characters until the next comma."

- `[^,]` = match any character except a comma
- `*` = zero or more times

**Note about character classes:** The square brackets `[]` create a character class that matches any single character from the set. When you add `^` inside the brackets (like `[^,]`), it creates a negated character class that matches any character NOT in the set. So `[^,]` matches letters, numbers, periods, spaces - anything except a comma.


To store the value, we put this into a named capture group:

```
(?P<long_w_spd>[^,]*)
```
We repeat this pattern for each of the ten fields.

### Step 3 – Add the Checksum

VBW ends with:
```
*HH
```
Where HH is a two-digit hexadecimal checksum. That can be matched with:

```
\*[0-9A-Fa-f]{2}
```

## 4. Final Complete Regex

This expression:

- Matches a valid VBW sentence
- Captures every field into a named variable
- Allows any field to be blank
- Requires a valid 2-digit hexadecimal checksum

### Single-line version
```
^\$VDVBW,(?P<long_w_spd>[^,]*),(?P<tran_w_spd>[^,]*),(?P<stat_w_spd>[^,]*),(?P<long_g_spd>[^,]*),(?P<tran_g_spd>[^,]*),(?P<stat_g_spd>[^,]*),(?P<tran_w_spd_stern>[^,]*),(?P<stat_w_spd_stern>[^,]*),(?P<tran_g_spd_stern>[^,]*),(?P<stat_g_spd_stern>[^,]*)\*[0-9A-Fa-f]{2}$
```
### Readable multi-line version (if your regex flavor supports verbose mode)
```
^
\$VDVBW,
(?P<long_w_spd>[^,]*),
(?P<tran_w_spd>[^,]*),
(?P<stat_w_spd>[^,]*),
(?P<long_g_spd>[^,]*),
(?P<tran_g_spd>[^,]*),
(?P<stat_g_spd>[^,]*),
(?P<tran_w_spd_stern>[^,]*),
(?P<stat_w_spd_stern>[^,]*),
(?P<tran_g_spd_stern>[^,]*),
(?P<stat_g_spd_stern>[^,]*)
\*[0-9A-Fa-f]{2}
$
```

Once matched, each field is available by its name. For example:

- `long_w_spd`
- `tran_w_spd`
- `stat_w_spd`, etc.

You can then convert, validate, or interpret them however your application requires.

## 5. Using the Results in CORIOLIX

CORIOLIX expects a Python list of regex strings. 
```python
['regex1']
```
For a single message type like VBW, you provide:

```python
['^\$VDVBW,(?P<long_w_spd>[^,]*),(?P<tran_w_spd>[^,]*),(?P<stat_w_spd>[^,]*),(?P<long_g_spd>[^,]*),(?P<tran_g_spd>[^,]*),(?P<stat_g_spd>[^,]*),(?P<tran_w_spd_stern>[^,]*),(?P<stat_w_spd_stern>[^,]*),(?P<tran_g_spd_stern>[^,]*),(?P<stat_g_spd_stern>[^,]*)\\*[0-9A-Fa-f]{2}$']
```

If a sensor outputs more than one message type (like VBW and DPT), you provide one regex per type:

```python
['regex1', 'regex2', ...]
```

