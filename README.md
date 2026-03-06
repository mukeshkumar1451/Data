In review_agent.py

Replace:

final_testcase = updated

with:

# Remove duplicate logout steps
lines = updated.split("\n")
seen_logout = False
clean_lines = []

for line in lines:
    if "log out from h2o-a" in line.lower():
        if seen_logout:
            continue
        seen_logout = True

    clean_lines.append(line)

final_testcase = "\n".join(clean_lines)
