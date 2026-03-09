import logging
import os
import re
from typing import Dict, List

logger = logging.getLogger(__name__)


class ReviewAgent:

    # ---------------------------------------------------------
    # Extract keywords from title
    # ---------------------------------------------------------
    def extract_title_keywords(self, title: str) -> List[str]:

        phrases = re.findall(r"[A-Za-z]+(?:\s[A-Za-z]+)?", title)

        keywords = []

        for p in phrases:
            if len(p) > 4:
                keywords.append(p.lower())

        return list(set(keywords))

    # ---------------------------------------------------------
    # Extract Test Step Descriptions
    # ---------------------------------------------------------
    def extract_step_descriptions(self, testcase):

        steps = []

        for line in testcase.split("\n"):

            if line.strip().startswith("Step"):

                parts = line.split("|")

                if len(parts) > 1:
                    steps.append(parts[1].strip().lower())

        return steps

    # ---------------------------------------------------------
    # Find missing keywords
    # ---------------------------------------------------------
    def find_missing_keywords(self, keywords, step_desc):

        text = " ".join(step_desc)

        missing = []

        for k in keywords:
            if k not in text:
                missing.append(k)

        return missing

    # ---------------------------------------------------------
    # Find historical step containing keyword
    # ---------------------------------------------------------
    def find_historical_step(self, historical_steps, keyword):

        for line in historical_steps.split("\n"):

            if keyword in line.lower():
                return line.strip()

        return None

    # ---------------------------------------------------------
    # Insert steps before logout
    # ---------------------------------------------------------
    def insert_steps_before_logout(self, testcase_lines, new_steps):

        logout_index = None

        for i, line in enumerate(testcase_lines):

            if "log out from h2o-a" in line.lower():
                logout_index = i
                break

        if logout_index is None:
            testcase_lines.extend(new_steps)
        else:
            testcase_lines = (
                testcase_lines[:logout_index]
                + new_steps
                + testcase_lines[logout_index:]
            )

        return testcase_lines

    # ---------------------------------------------------------
    # Renumber steps
    # ---------------------------------------------------------
    def renumber_steps(self, testcase_lines):

        step_counter = 1
        updated_lines = []

        for line in testcase_lines:

            if line.strip().startswith("Step"):

                new_line = re.sub(
                    r"Step\s*\d+",
                    f"Step {step_counter:02d}",
                    line
                )

                updated_lines.append(new_line)

                step_counter += 1

            else:
                updated_lines.append(line)

        return updated_lines

    # ---------------------------------------------------------
    # Save testcase log
    # ---------------------------------------------------------
    def save_log(self, story_id, channel, testcase):

        os.makedirs("logs", exist_ok=True)

        file_path = f"logs/{story_id}_{channel}_testcase.txt"

        with open(file_path, "w", encoding="utf-8") as f:
            f.write(testcase)

        logger.info(f"Saved testcase log: {file_path}")

    # ---------------------------------------------------------
    # MAIN RUN
    # ---------------------------------------------------------
    def run(self, state: Dict) -> Dict:

        logger.info("Review Agent running")

        title = state["title"]

        keywords = self.extract_title_keywords(title)

        for channel, testcase in state["llm_outputs"].items():

            step_desc = self.extract_step_descriptions(testcase)

            missing = self.find_missing_keywords(keywords, step_desc)

            if not missing:
                self.save_log(
                    state["user_story_id"],
                    channel,
                    testcase
                )
                continue

            logger.warning(f"{channel} missing keywords: {missing}")

            historical = state["channel_context"][channel]["historical_steps"]

            testcase_lines = testcase.split("\n")

            new_steps = []

            for keyword in missing:

                hist_step = self.find_historical_step(
                    historical,
                    keyword
                )

                if hist_step:

                    new_steps.append(
                        f"Step XX | {hist_step} | NA | NA | NA | NA"
                    )

            if new_steps:

                logger.info(f"{channel} → Added Steps:")

                for s in new_steps:
                    logger.info(s)

                testcase_lines = self.insert_steps_before_logout(
                    testcase_lines,
                    new_steps
                )

                testcase_lines = self.renumber_steps(testcase_lines)

                testcase = "\n".join(testcase_lines)

                state["llm_outputs"][channel] = testcase

            self.save_log(
                state["user_story_id"],
                channel,
                testcase
            )

        return state
