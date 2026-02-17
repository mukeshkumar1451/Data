tc_list = self._parse_llm_output(llm_text)

for tc_data in tc_list:

    tc_id = f"US_{user_story_id}_TC_{tc_counter[channel]:02d}"
    precondition = self._format_precondition(channel, setup_text)

    new_row = self._write_testcase(ws, row, tc_id, tc_data, precondition)

    row = new_row
    tc_counter[channel] += 1
