# Course Scheduling Skill

Provides information about course schedules and academic calendar for Haaga-Helia. All dates and times are in Helsinki timezone (EET/EEST).

## Course Scheduling API

### Fetch course details and events

```bash
curl 'https://lukkarit.haaga-helia.fi/rest/realization/{COURSE_CODE}'
```

**Parameters:**
- `COURSE_CODE`: Course implementation code (e.g., `SOF004AS2A-3033`)

**Response includes:**
- `name`: Course name
- `code`: Course implementation code
- `teaching_language`: Instruction language
- `scope_amount`: Credit points (op)
- `evaluation_scale`: Grading scale (H-5, Pass/Fail, etc.)
- `office`: Campus location
- `start_date`, `end_date`: Course dates (YYYY-MM-DD)
- `enrollment_start_date`, `enrollment_end_date`: Registration window
- `tgroup`: Target student groups
- `learning_material`: Links and descriptions
- `further_information`: Additional details
- `events`: Array of scheduled events

**Event structure:**
```json
{
  "event_id": 584693,
  "start_date": "2027-01-18 11:00",
  "end_date": "2027-01-18 13:45",
  "code": ["SOF004AS2A-3033"],
  "subject": "SOF004AS2A-3033 Python-ohjelmointi",
  "location": [
    {
      "class": "PA/2008-BYOD_50",
      "name": "BYOD-luokka TRAL ry",
      "parent": "Pasila"
    }
  ],
  "student_groups": ["IT4PASOF1", "IT2PAICI1"]
}
```

**Example:**
```bash
curl 'https://lukkarit.haaga-helia.fi/rest/realization/SOF004AS2A-3033' | jq '.events[] | {start_date, end_date, location}'
```

## Academic Calendar

The academic calendar is published at:
```
https://www.haaga-helia.fi/fi/opiskelu/lukuvuoden-paivamaarat
```

**Academic year structure:**

### Fall Semester (1.8. - 31.12.)
- **Teaching dates**: Mid-August to mid-December
- **Period 1**: August-October
- **Period 2**: October-December
- **Intensive weeks**: Before/between/after periods
- **Non-teaching days**: Christmas break (21.12. - 1.1.)

**Example for 2026:**
```
Intensiiviviikko: 10. – 14.8.2026 (vko 33)
1. periodi: 17.8. – 9.10.2026 (vkot 34 – 41)
Intensiiviviikko: 12. – 16.10.2026 (vko 42)
2. periodi: 19.10. – 11.12.2026 (vkot 43 – 50)
Intensiiviviikko: 14. – 18.12.2026 (vko 51)
Opetuksettomat päivät: 21.12.2026 – 1.1.2027
```

### Spring Semester (1.1. - 31.7.)
- **Teaching dates**: Mid-January to mid-May
- **Period 3**: January-March (with break in February)
- **Period 4**: March-May
- **Period 5 (Summer)**: May-August
- **Intensive weeks**: Before/between periods
- **Non-teaching days**: Public holidays (New Year, Easter, May Day)

**Example for 2027:**
```
Intensiiviviikot: 4. – 15.1.2027 (vkot 1 – 2)
3. periodi: 18.1. – 19.3.2027 (vkot 3 – 7 & 9 – 11)
Intensiiviviikko: 22. – 26.2.2027 (vko 8)
Intensiiviviikko: 22. – 25.3.2027 (vko 12)
4. periodi: 29.3. – 21.5.2027 (vkot 13 – 20)
Intensiiviviikot: 24.5. – 11.6.2027 (vkot 21 – 23)
5. periodi: 24.5. – 20.8.2027 (vkot 21 – 33)

Opetuksettomat päivät: 6.1., 26.3., 29.3., 6.5.
```

## Common Queries

### Get all events for a course in a date range

```bash
curl 'https://lukkarit.haaga-helia.fi/rest/realization/SOF004AS2A-3033' | jq '.events[] | select(.start_date >= "2027-01-20" and .start_date <= "2027-01-27")'
```

### Extract course schedule (simple calendar view)

```bash
curl 'https://lukkarit.haaga-helia.fi/rest/realization/SOF004AS2A-3033' | jq '.events | sort_by(.start_date) | .[] | "\(.start_date | .[0:10]) \(.start_date | .[11:16])-\(.end_date | .[11:16]): \(.location[0].name)"'
```

### Check course enrollment dates

```bash
curl 'https://lukkarit.haaga-helia.fi/rest/realization/SOF004AS2A-3033' | jq '{enrollment_start: .enrollment_start_date, enrollment_end: .enrollment_end_date, course_start: .start_date}'
```

### List all locations used by a course

```bash
curl 'https://lukkarit.haaga-helia.fi/rest/realization/SOF004AS2A-3033' | jq '.events[].location[] | {class, name, campus: .parent}' | sort | uniq
```

### List all student groups in a course

```bash
curl 'https://lukkarit.haaga-helia.fi/rest/realization/SOF004AS2A-3033' | jq '.events[].student_groups[]' | sort | uniq
```

## Timezone Notes

- All times in the scheduling API are in **Helsinki local time** (EET/EEST)
- Dates are in ISO 8601 format: `YYYY-MM-DD` or `YYYY-MM-DD HH:MM`
- EET = UTC+2 (winter)
- EEST = UTC+3 (summer, roughly late March to late October)

## Campus-Specific Scheduling

Some intensive weeks vary by campus (e.g., Vierumäki vs. Pasila). Check the academic calendar source for campus-specific notes:

```
Intensiiviviikko: 12. – 16.10.2026 (vko 42), paitsi Vierumäki 19. – 23.10.2026 (vko 43)
```

The main campus is **Pasila** unless otherwise specified.

## Course Enrollment

Students can find enrollment instructions at:
```
https://www.haaga-helia.fi/fi/toteutuksille-ilmoittautuminen
```

Students are already familiar with the enrollment process and do not require additional guidance.

## Related Resources

- **Lukkarit scheduling system**: https://lukkarit.haaga-helia.fi/
- **Academic calendar**: https://www.haaga-helia.fi/fi/opiskelu/lukuvuoden-paivamaarat
- **Course enrollment instructions**: https://www.haaga-helia.fi/fi/toteutuksille-ilmoittautuminen
- **Student information portal**: https://www.haaga-helia.fi/
