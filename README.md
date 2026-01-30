import express from "express";
import axios from "axios";
import dotenv from "dotenv";
import xlsx from "xlsx";

dotenv.config();

const app = express();
app.use(express.json());

/* ---------------- GRAPH TOKEN ---------------- */

async function getGraphToken() {
  const url = `https://login.microsoftonline.com/${process.env.TENANT_ID}/oauth2/v2.0/token`;

  const params = new URLSearchParams();
  params.append("client_id", process.env.CLIENT_ID);
  params.append("client_secret", process.env.CLIENT_SECRET);
  params.append("scope", "https://graph.microsoft.com/.default");
  params.append("grant_type", "client_credentials");

  const res = await axios.post(url, params);
  return res.data.access_token;
}

/* ---------------- AZURE DEVOPS USER STORY ---------------- */

async function getUserStory(id) {
  const url = `https://dev.azure.com/${process.env.ADO_ORG}/${process.env.ADO_PROJECT}/_apis/wit/workitems/${id}?api-version=7.0`;

  const res = await axios.get(url, {
    headers: {
      Authorization:
        "Basic " +
        Buffer.from(":" + process.env.ADO_PAT).toString("base64"),
    },
  });

  return res.data.fields;
}

/* ---------------- SHAREPOINT SEARCH ---------------- */

async function searchSharePoint(text, token) {
  const url = "https://graph.microsoft.com/v1.0/search/query";

  const body = {
    requests: [
      {
        entityTypes: ["driveItem"],
        query: { queryString: text },
      },
    ],
  };

  const res = await axios.post(url, body, {
    headers: { Authorization: `Bearer ${token}` },
  });

  return res.data;
}

/* ---------------- READ EXCEL FROM SHAREPOINT ---------------- */

async function readExcelFromSharePoint(driveId, itemId, token) {
  const url = `https://graph.microsoft.com/v1.0/drives/${driveId}/items/${itemId}/content`;

  const response = await axios.get(url, {
    headers: { Authorization: `Bearer ${token}` },
    responseType: "arraybuffer",
  });

  const workbook = xlsx.read(response.data, { type: "buffer" });
  const sheet = workbook.Sheets[workbook.SheetNames[0]];
  const rows = xlsx.utils.sheet_to_json(sheet, { defval: "" });

  return rows;
}

/* ---------------- GROUP INTO TEST SCRIPT STRUCTURE ---------------- */

function groupTestScripts(rows) {
  const grouped = {};

  rows.forEach((row) => {
    const id = row["Test Case ID / Test Script ID"];
    if (!id) return;

    if (!grouped[id]) {
      grouped[id] = {
        testCaseId: id,
        preCondition: row["Pre-Condition & Assumptions"],
        requirementMapping: row["Requirement Mapping"],
        steps: [],
      };
    }

    grouped[id].steps.push({
      stepNo: row["Test Step No."],
      description: row["Test Step Description"],
      screen: row["Screen Name"],
      data: row["Test Data"],
      expected: row["Expected Results"],
    });
  });

  return Object.values(grouped);
}

/* ---------------- MAIN API ---------------- */

app.post("/find-testcases", async (req, res) => {
  try {
    const { userStoryId } = req.body;

    console.log("Fetching ADO User Story...");
    const story = await getUserStory(userStoryId);

    console.log("Getting Graph token...");
    const token = await getGraphToken();

    console.log("Searching SharePoint...");
    const searchText = story["System.Title"];
    const searchResults = await searchSharePoint(searchText, token);

    const hits =
      searchResults?.value?.[0]?.hitsContainers?.[0]?.hits || [];

    let allTestScripts = [];

    for (const hit of hits) {
      const resource = hit.resource;

      if (!resource.name.endsWith(".xlsx")) continue;

      const driveId = resource.parentReference.driveId;
      const itemId = resource.id;

      console.log(`Reading Excel: ${resource.name}`);

      const rows = await readExcelFromSharePoint(
        driveId,
        itemId,
        token
      );

      const scripts = groupTestScripts(rows);
      allTestScripts.push(...scripts);
    }

    res.json({
      userStory: story,
      matchedTestScripts: allTestScripts,
    });
  } catch (e) {
    console.error(e);
    res.status(500).send(e.message);
  }
});

/* ---------------- START SERVER ---------------- */

app.listen(3000, () => {
  console.log("MCP ADO + SharePoint server running on port 3000");
});
