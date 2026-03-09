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
    # Extract step descriptions
    # ---------------------------------------------------------
    def extract_step_descriptions(self, testcase):

        steps = []

        for line in testcase.split("\n"):

            if line.strip().startswith("Step"):

                parts = line.split("|")

                if len(parts) > 1:
                    steps.append(parts[1].lower())

        return steps

    # ---------------------------------------------------------
    # Find missing keywords
    # ---------------------------------------------------------
    def find_missing_keywords(self, keywords, step_descriptions):

        text = " ".join(step_descriptions)

        missing = []

        for k in keywords:
            if k not in text:
                missing.append(k)

        return missing

    # ---------------------------------------------------------
    # Search historical steps
    # ---------------------------------------------------------
    def search_historical_steps(self, historical_steps, keyword):

        for line in historical_steps.split("\n"):

            if keyword in line.lower():
                return line

        return None

    # ---------------------------------------------------------
    # Insert new step
    # ---------------------------------------------------------
      def create_new_step(self, step_number, historical_step):

         desc = historical_step.get("Description", "")
         screen = historical_step.get("Screen", "")
         expected = historical_step.get("Expected Result", "")

       return (
          f"Step {step_number:02d} | "
        f"{desc} | "
        f"{screen} | "
        f"NA | "
        f"{expected} | "
        f"NA"
    )

    # ---------------------------------------------------------
    # Save testcase
    # ---------------------------------------------------------
    def save_log(self, story_id, channel, testcase):

        os.makedirs("logs", exist_ok=True)

        file = f"logs/{story_id}_{channel}_testcase.txt"

        with open(file, "w", encoding="utf-8") as f:
            f.write(testcase)

        logger.info(f"Saved testcase log: {file}")

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
                continue

            logger.warning(f"{channel} missing keywords: {missing}")

            historical = state["channel_context"][channel]["historical_steps"]

            lines = testcase.split("\n")

            step_number = len([l for l in lines if l.startswith("Step")]) + 1

            added_steps = []

            for keyword in missing:

                hist_step = self.search_historical_steps(historical, keyword)

                if hist_step:

                    new_step = self.create_new_step(step_number, hist_step)

                    added_steps.append(new_step)

                    step_number += 1

            if added_steps:

                logger.info(f"{channel} → Added Steps:")

                for s in added_steps:
                    logger.info(s)

                testcase = testcase + "\n" + "\n".join(added_steps)

            state["llm_outputs"][channel] = testcase

            self.save_log(state["user_story_id"], channel, testcase)

        return state
