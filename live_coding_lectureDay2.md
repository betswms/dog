# LIVE CODING LECTURE SCRIPT — Python + PostgreSQL + NiceGUI

**Date Generated:** 2026-03-19

---

# 🎯 Lecture Goal
Walk students step-by-step through:
- Building a course lookup function
- Completing a registration system
- Improving logic with constraints
- Transitioning into a web app using NiceGUI

---

# ⏱️ PART 1 — test_students.py (Detailed Live Script)

## Instructor Script (say this out loud)

“Alright everyone, open `test_students.py`.

We’re going to build something useful today: instead of just listing students, we want to answer a real question:

👉 *What classes is a student taking?*

This means we need to combine data from multiple tables.”

---

## STEP 1 — Start with existing function

Type slowly:

```python
def show_student_courses():
    student_id = int(input("Enter student ID: "))
```

### SAY:
“Notice I convert input to an integer. Database IDs are numbers — if we forget this, we’ll get errors later.”

---

## STEP 2 — Write SQL query

```python
    cur.execute("""
        SELECT course_id, course_name
        FROM courses
        NATURAL JOIN enroll
        WHERE student_id = %s
    """, [student_id])
```

### SAY:
“Two important things here:
1. NATURAL JOIN connects tables based on matching column names
2. %s is a placeholder — we DO NOT put values directly into SQL strings”

---

## STEP 3 — Fetch results

```python
    rows = cur.fetchall()
```

### SAY:
“This pulls all matching rows into Python.”

---

## STEP 4 — Display results

```python
    for course in rows:
        print(course['course_id'], course['course_name'])
```

---

## STEP 5 — Improve output (Live refactor)

Replace function with:

```python
def show_student_courses():
    student_id = int(input("Enter student ID: "))

    cur.execute("""
        SELECT department, course_number, course_section, course_name, start_time
        FROM enroll
        NATURAL JOIN courses
        WHERE student_id = %s
    """, [student_id])

    rows = cur.fetchall()

    if len(rows) == 0:
        print("No courses found.")
        return

    print(f"Schedule for student {student_id}:")
    for course in rows:
        print(f"{course['department']}-{course['course_number']}: {course['course_name']} at {course['start_time']}")
```

---

# ⏱️ PART 2 — register.py (DETAILED LIVE BUILD)

## SAY:
“Now we’re moving from single functions into a small application.”

---

## STEP 1 — Start function

```python
def enroll_student_in_course():
```

---

## STEP 2 — Show students

```python
    list_students()
```

### SAY:
“We always show choices before asking the user.”

---

## STEP 3 — Get student

```python
    chosen_student = int(input("Enter student id: "))
```

---

## STEP 4 — Show courses

```python
    list_courses()
```

---

## STEP 5 — Get course

```python
    chosen_course = int(input("Enter course id: "))
```

---

## STEP 6 — Insert into database

```python
    cur.execute(
        "INSERT INTO enroll (student_id, course_id) VALUES (%s, %s)",
        [chosen_student, chosen_course]
    )
```

---

## STEP 7 — Commit

```python
    conn.commit()
```

### SAY:
“If you forget commit, NOTHING saves.”

---

## STEP 8 — Confirmation

```python
    print("Enrollment successful.")
```

---

# ⏱️ PART 3 — ADVANCED LOGIC (Time Conflict)

## SAY:
“Now let’s make this smarter.”

---

## STEP 1 — Get existing schedule

```python
cur.execute("""
    SELECT start_time
    FROM enroll
    NATURAL JOIN courses
    WHERE student_id = %s
""", [chosen_student])
```

---

## STEP 2 — Filter courses

```python
cur.execute("""
    SELECT *
    FROM courses
    WHERE start_time NOT IN (
        SELECT start_time
        FROM enroll
        NATURAL JOIN courses
        WHERE student_id = %s
    )
""", [chosen_student])
```

---

## STEP 3 — Display filtered courses

```python
courses = cur.fetchall()

for course in courses:
    print(course['course_id'], course['course_name'], course['start_time'])
```

---

## SAY:
“This is our first example of business logic inside SQL.”

---

# ⏱️ PART 4 — NICEGUI (DETAILED SCRIPT)

## SAY:
“Now we switch from terminal to web apps.”

---

## STEP 1 — Install

```bash
pip install nicegui
```

---

## STEP 2 — First app

```python
from nicegui import ui

ui.label('Welcome to NiceGUI!')
ui.run()
```

---

## SAY:
“This starts a local web server and opens your browser.”

---

## STEP 3 — Explain event-driven programming

### SAY:
“Terminal:
- program runs step-by-step

Web:
- waits for user actions (clicks)”

---

# ⏱️ PART 5 — CONNECT TO DATABASE

## STEP 1 — Fetch data

```python
def get_students():
    cur.execute("SELECT student_id, first_name, last_name FROM students")
    return cur.fetchall()
```

---

## STEP 2 — Display in table

```python
@ui.page('/students')
def students_page():
    data = get_students()
    ui.table(rows=data)
```

---

## STEP 3 — Add input + button

```python
student_id = ui.input("Student ID")
first_name = ui.input("First Name")

ui.button("Add Student", on_click=lambda: add_student(
    student_id.value,
    first_name.value
))
```

---

## SAY:
“This replaces input() and print() with UI elements.”

---

# 🔥 FINAL TEACHING POINT

### SAY:
“The database logic did NOT change.

Only the interface changed.”

---

# 🎯 END OF CLASS QUESTIONS

- Why do we use commit?
- Why parameterized SQL?
- What changed with NiceGUI?
- What stayed the same?

---

# ✅ DONE
