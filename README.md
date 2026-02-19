    # ---------------------------------------------------------
    # LangGraph entry
    # ---------------------------------------------------------
    def run(self, state: dict) -> dict:
        logger.info("📄 Excel Export Agent started")

        os.makedirs(self.output_dir, exist_ok=True)
        wb = load_workbook(self.template_path)

        story_channels = state.get("channels", [])
        setup_map = state.get("channel_setup", {})
        llm_outputs = state.get("llm_outputs", {})

        logger.info(f"Detected channels: {story_channels}")
        logger.info(f"Incoming setup_map keys: {list(setup_map.keys())}")

        # ---------------------------------------------------------
        # REMOVE UNUSED SHEETS
        # ---------------------------------------------------------
        for sheet in list(wb.sheetnames):
            if sheet not in story_channels:
                logger.info(f"🧹 Removing unused sheet: {sheet}")
                std = wb[sheet]
                wb.remove(std)

        # Ensure channel sheet exists (safety)
        for ch in story_channels:
            if ch not in wb.sheetnames:
                logger.info(f"📄 Creating missing sheet: {ch}")
                wb.create_sheet(ch)

        # ---------------------------------------------------------
        # Dynamic trackers (ONLY for active channels)
        # ---------------------------------------------------------
        sheets = {name: wb[name] for name in wb.sheetnames}
        row_tracker = {ch: 2 for ch in story_channels}
        tc_counter = {ch: 1 for ch in story_channels}

        user_story_id = state["user_story_id"]

        # ---------------------------------------------------------
        # WRITE TESTCASES
        # ---------------------------------------------------------
        for channel in story_channels:

            llm_text = llm_outputs.get(channel)
            if not llm_text:
                logger.warning(f"⚠️ No LLM output for {channel}, skipping")
                continue

            ws = sheets[channel]

            tc_data = self._parse_llm_output(llm_text)

            row = row_tracker[channel]
            tc_id = f"US_{user_story_id}_TC_{tc_counter[channel]:02d}"

            setup_text = setup_map.get(channel, "")
            precondition = self._format_precondition(channel, setup_text)

            logger.info(f"\nFormatted precondition for {channel}:\n{precondition}\n")

            new_row = self._write_testcase(ws, row, tc_id, tc_data, precondition)

            row_tracker[channel] = new_row
            tc_counter[channel] += 1

        # ---------------------------------------------------------
        # SAVE
        # ---------------------------------------------------------
        output_file = os.path.join(
            self.output_dir,
            f"Indiv_US_{user_story_id}_Test Scripts_v1.0.xlsx"
        )

        wb.save(output_file)
        logger.info(f"✅ Excel generated: {output_file}")

        state["excel_output"] = output_file
        return state
