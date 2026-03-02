# YES Conditional Only
if field_type == "dropdown" and "Yes" in values:

    block.append(f'Select "Yes" from the "{name}" dropdown.')

    if visibility:
        block.append(
            f"Verify that dependent fields are visible when: {visibility}."
        )
    else:
        block.append(
            "Verify that dependent fields or sections are displayed."
        )
