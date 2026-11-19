# Practical Lab 1 – Hands-On Exercises

## Exercise 1: Degree Honors Rule

**Solution:**

Add this rule to `UNIVERSITY_KB["rules"]`:

```python
{
    "id": "RULE_07",
    "conditions": {
        "cgpa_min": 3.70,
        "disciplinary_warnings": 0
    },
    "condition_type": "degree_honors",
    "grade": "N/A",
    "status": "Graduated with Magna Cum Laude Honors",
    "action": "Recognized for outstanding academic performance."
}
```

**Explanation:** The student receives Magna Cum Laude Honors if their CGPA is at least 3.70 and they have zero disciplinary warnings.

## Exercise 2: Tracing Forward Chaining

**Solution:**

Add print statements to `evaluate_student()` to display each evaluated rule condition.

```python
def evaluate_student(student_record, kb):

    print(f"\nEvaluating Student: {student_record['student_name']}")

    # Check Attendance Rule
    print("\nChecking Attendance Rule...")

    if student_record["attendance"] < 75:
        print("Attendance Condition: True")

        rule = next(
            r for r in kb["rules"]
            if r["condition_type"] == "attendance_below"
        )

        print("Matched Rule:", rule["id"])

        return {
            "rule_id": rule["id"],
            "grade": rule["grade"],
            "status": rule["status"],
            "action": rule["action"]
        }

    print("Attendance Condition: False")

    # Check Marks Range Rules
    marks = student_record["total_marks"]

    for rule in kb["rules"]:

        if rule["condition_type"] == "marks_range":

            minimum = rule["conditions"]["marks_min"]
            maximum = rule["conditions"]["marks_max"]

            print(f"\nChecking {rule['id']}:")
            print(f"{minimum} <= {marks} <= {maximum}")

            if minimum <= marks <= maximum:

                print("Condition: True")
                print("Matched Rule:", rule["id"])

                return {
                    "rule_id": rule["id"],
                    "grade": rule["grade"],
                    "status": rule["status"],
                    "action": rule["action"]
                }

            print("Condition: False")

    return {
        "rule_id": "UNKNOWN",
        "grade": "N/A",
        "status": "Invalid Score Entry",
        "action": "Verify submitted marks."
    }
```

**Explanation:** Forward chaining checks available student facts against predefined rules and prints each condition until a matching rule produces a result.
