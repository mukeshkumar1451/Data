import axios from "axios";
import dotenv from "dotenv";
import fs from "fs";

dotenv.config();

/* ------------ Get ID from command line ------------ */
const userStoryId = process.argv[2];

if (!userStoryId) {
  console.log("❌ Please provide User Story ID");
  console.log("Example: node server.js 718521");
  process.exit(1);
}

/* ------------ Fetch User Story ------------ */
async function getUserStoryWithAttachments(id) {
  const url = `https://dev.azure.com/${process.env.ADO_ORG}/${process.env.ADO_PROJECT}/_apis/wit/workitems/${id}?$expand=relations&api-version=7.0`;

  const res = await axios.get(url, {
    headers: {
      Authorization:
        "Basic " +
        Buffer.from(":" + process.env.ADO_PAT).toString("base64"),
    },
  });

  return res.data;
}

/* ------------ Download Attachment ------------ */
async function downloadAttachment(url, fileName) {
  const response = await axios.get(url, {
    headers: {
      Authorization:
        "Basic " +
        Buffer.from(":" + process.env.ADO_PAT).toString("base64"),
    },
    responseType: "arraybuffer",
  });

  const filePath = `./attachments/${fileName}`;
  fs.writeFileSync(filePath, response.data);
  console.log(`📎 Attachment saved: ${filePath}`);
}

/* ------------ Main Execution ------------ */
async function run() {
  try {
    console.log(`\nFetching User Story: ${userStoryId}\n`);

    const userStory = await getUserStoryWithAttachments(userStoryId);

    console.log("========== USER STORY DETAILS ==========");
    console.log("ID:", userStory.id);
    console.log("Title:", userStory.fields["System.Title"]);
    console.log("Description:", userStory.fields["System.Description"]);
    console.log(
      "Acceptance Criteria:",
      userStory.fields["Microsoft.VSTS.Common.AcceptanceCriteria"]
    );
    console.log("=========================================\n");

    if (userStory.relations) {
      for (const rel of userStory.relations) {
        if (rel.rel === "AttachedFile") {
          const fileUrl = rel.url;
          const fileName = rel.attributes.name;

          console.log("Downloading attachment:", fileName);
          await downloadAttachment(fileUrl, fileName);
        }
      }
    } else {
      console.log("No attachments found.");
    }
  } catch (e) {
    console.error("Error:", e.message);
  }
}

run();
