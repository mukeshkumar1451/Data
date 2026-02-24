def run(self, state: dict) -> dict:

    logger.info("Excel Export Agent started")

    os.makedirs(self.output_dir, exist_ok=True)
    wb = load_workbook(self.template_path)

    user_story_id = state["user_story_id"]
    tc_counter = 1

    for channel, llm_text in state["llm_outputs"].items():

        if channel not in wb.sheetnames:
            logger.warning(f"Sheet {channel} not found in template")
            continue

        ws = wb[channel]   # ✅ Correct sheet selection
        row = 2            # Always start at row 2

        parsed = self._parse_llm_output(llm_text)

        logger.info(f"{channel} -> Parsed {len(parsed['steps'])} steps")

        if not parsed["steps"]:
            continue

        # ✅ Get precondition from retrieval layer
        precondition = state["selected_preconditions"].get(channel, "")

        tc_id = f"US_{user_story_id}_{channel}_TC_{tc_counter:02d}"

        for idx, step in enumerate(parsed["steps"]):

            ws.cell(row, 1).value = tc_id if idx == 0 else ""
            ws.cell(row, 2).value = f"{user_story_id}-{channel}"
            ws.cell(row, 3).value = parsed["scenario"] if idx == 0 else ""
            ws.cell(row, 4).value = parsed["script"] if idx == 0 else ""
            ws.cell(row, 5).value = precondition if idx == 0 else ""

            ws.cell(row, 6).value = step["step_no"]
            ws.cell(row, 7).value = step["desc"]
            ws.cell(row, 8).value = step["screen"]
            ws.cell(row, 9).value = step["data"]
            ws.cell(row, 10).value = step["expected"]

            ws.cell(row, 15).value = parsed["requirement"] if idx == 0 else ""

            row += 1

        tc_counter += 1

    output_file = os.path.join(
        self.output_dir,
        f"Indiv_US_{user_story_id}_Test_Scripts_v1.0.xlsx"
    )

    wb.save(output_file)

    logger.info(f"Excel generated: {output_file}")
    state["excel_output"] = output_file
    return state
