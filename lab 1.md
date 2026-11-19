# Practical Lab 1 – Hands-On Exercises

## Exercise 1: Degree Honors Rule

*Solution:*

Add this rule to `UNIVERSITY_KB["rules"]`:

```json
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

*Explanation:* The student receives Magna Cum Laude Honors if their CGPA is at least 3.70 and they have zero disciplinary warnings.

---

## Exercise 2: Tracing Forward Chaining

*Solution:*

Add `print` statements to `evaluate_student()` to display each evaluated rule condition, handle the new `degree_honors` rule, and safely retrieve rules without risking a `StopIteration` error:

```python
def evaluate_student(student_record, kb):
    print(f"\nEvaluating Student: {student_record['student_name']}")

    # 1. Check Attendance Rule
    print("\nChecking Attendance Rule...")
    if student_record["attendance"] < 75:
        print("Attendance Condition: True")
        rule = next(
            (r for r in kb["rules"] if r["condition_type"] == "attendance_below"),
            None
        )
        if rule:
            print("Matched Rule:", rule["id"])
            return {
                "rule_id": rule["id"],
                "grade": rule["grade"],
                "status": rule["status"],
                "action": rule["action"]
            }
    print("Attendance Condition: False")

    # 2. Check Degree Honors Rule
    if "cgpa" in student_record and "disciplinary_warnings" in student_record:
        for rule in kb["rules"]:
            if rule["condition_type"] == "degree_honors":
                cgpa_min = rule["conditions"]["cgpa_min"]
                warnings_req = rule["conditions"]["disciplinary_warnings"]
                print(f"\nChecking {rule['id']} (Honors):")
                print(
                    f"CGPA {student_record['cgpa']} >= {cgpa_min} and "
                    f"Warnings {student_record['disciplinary_warnings']} == {warnings_req}"
                )

                if (
                    student_record["cgpa"] >= cgpa_min
                    and student_record["disciplinary_warnings"] == warnings_req
                ):
                    print("Condition: True")
                    print("Matched Rule:", rule["id"])
                    return {
                        "rule_id": rule["id"],
                        "grade": rule["grade"],
                        "status": rule["status"],
                        "action": rule["action"]
                    }
                print("Condition: False")

    # 3. Check Marks Range Rules
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

    # Fallback if no rules matched
    return {
        "rule_id": "UNKNOWN",
        "grade": "N/A",
        "status": "Invalid Score Entry",
        "action": "Verify submitted marks."
    }
```

*Explanation:* Forward chaining checks available student facts against predefined rules and prints each condition until a matching rule produces a result.
