{
  "servers": {
    "ado-agent": {
      "command": "python",
      "args": ["mcp_tools/ado_server.py"],
      "env": {
        "ADO_ORG": "your_org",
        "ADO_PROJECT": "your_project",
        "ADO_PAT": "your_pat"
      }
    }
  }
}
