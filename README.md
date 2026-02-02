import express from "express";
import axios from "axios";
import dotenv from "dotenv";
import fs from "fs";

dotenv.config();

const app = express();
app.use(express.json());

/* ------------ Extract Work Item ID from URL ------------ */
function extractWorkItemIdFromUrl(url) {
  const match = url.match(/workitems\/edit\/(\d+)/);
  if (match && match[1]) {
    return match[1];
  }
  throw new Error("Invalid Azure DevOps User Story URL");
}

/* ------------ Get User Story with Relations ------------ */
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

  console.log(`Attachment saved: ${filePath}`);
}

/* ------------ API Endpoint ------------ */
app.post("/fetch-userstory", async (req, res) => {
  try {
    const { userStoryUrl } = req.body;

    const id = extractWorkItemIdFromUrl(userStoryUrl);
    console.log("\nExtracted Work Item ID:", id);

    const userStory = await getUserStoryWithAttachments(id);

    console.log("\n========== USER STORY DETAILS ==========");
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

    res.json({
      message: "User story fetched. Check console logs and attachments folder.",
    });
  } catch (e) {
    console.error("Error:", e.message);
    res.status(500).send(e.message);
  }
});

/* ------------ Start Server ------------ */
app.listen(3000, () => {
  console.log("ADO MCP Test Server running on port 3000");
});
