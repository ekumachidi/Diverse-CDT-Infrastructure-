# 📖 How-To: Using Data in Your Observable Notebooks

**Audience:** PhD Students

> This guide covers the two standard, RSE-approved methods for loading data into your Observable projects. The method you choose depends on your data's classification: **L1 (Public)** or **L2 (Internal)**.

### What Kind of Data Can I Use?

You can use most modern "flat file" data types. The most common ones you'll use are:

- **CSV (Comma-Separated Values):** The standard for tabular data. Observable parses this into an array of objects.
- **JSON (JavaScript Object Notation):** The native format for the web. This is perfect for nested data, lists, or pre-structured objects.
- **TSV (Tab-Separated Values):** A common alternative to CSV.
- **Text files (.txt):** For loading raw text.
- **Excel files (.xlsx):** Observable can even parse these directly, but we recommend exporting to CSV first for better performance and standardization.

---

## Method 1: The L1 (Public) Way 🌍

This is the best method for open data, public datasets, or any data you are happy to share with the world. It involves hosting your file in a **public GitHub repository**.

**This is the preferred method for creating a public portfolio.**

### Step-by-Step Guide (L1)

**Step 1: Put Your Data on GitHub**

1.  Navigate to our CDT GitHub Organization.
2.  Create a **new repository**.
3.  Make sure the repository's visibility is set to **Public**.
4.  Upload your data file (e.g., `my_public_data.csv`) to this repository.

**Step 2: Get the "Raw" URL**
This is the most important step. You cannot use the normal GitHub URL.

1.  In your public repository, click on your data file.

2.  On the file's page, find and click the button labeled **"Raw"**.

3.  Your browser will open a new page showing just the raw text of your file.

4.  **Copy this URL** from your browser's address bar. It should look something like this:
    `https://raw.githubusercontent.com/YOUR_ORG/YOUR_REPO/main/my_public_data.csv`

**Step 3: Load the Data in Observable**

1.  In your Observable notebook, create a new JavaScript cell.
2.  Use the `FileAttachment` command, pasting your "Raw" URL inside the parentheses.
3.  Call the correct parser (like `.csv()` or `.json()`) to load the data.

<!-- end list -->

```javascript
// Example for loading a CSV file:
myPublicData = FileAttachment("https.../my_public_data.csv").csv();

// Example for loading a JSON file:
myPublicJson = FileAttachment("https.../my_public_data.json").json();
```

When you run the cell, Observable will fetch the data from your public GitHub repo and load it into the `myPublicData` variable, ready for visualization.

---

## Method 2: The L2 (Internal) Way 🔐

This is the standard, secure method for **internal-only** data, such as some course assignments, draft data, or non-sensitive partner data that should only be seen by the CDT cohort and staff.

This method uses a **Personal Access Token (PAT)** and Observable's **Secrets** manager to securely access a **private GitHub repository**.

> **SECURITY-BY-DEFAULT:** This is our standard for a reason. Observable **will not allow you to use a Secret in a public notebook.** This process _enforces_ privacy, removing the risk of you accidentally exposing sensitive data.

### Step-by-Step Guide (L2)

**Step 1: Get Your Token**

1.  For any project or assignment with L2 data, **RSE (Chidi)** will create a private repository for you.
2.  RSE will then provide you with a **GitHub Personal Access Token (PAT)**. This token is a secure password that _only_ grants access to that specific repository.

**Step 2: Create a _Private_ Notebook**

1.  This method _only_ works in a **private** notebook.
2.  Create a new notebook and immediately **Share** it with our private CDT team (e.g., `@cdt-cohort-1`).
3.  **Do not** make the notebook public.

**Step 3: Store the Token as a Secret**

1.  In your **private** notebook, click the **three-dot menu (`...`)** in the top-right corner.
2.  Select **"Secrets"** from the menu.
3.  Click **"New secret"**.
    - **Name:** `GITHUB_TOKEN` (Must be _exactly_ this)
    - **Value:** Paste the token I gave you (it will look like `ghp_...`).
4.  Click **"Save"**.

**Step 4: Use the RSE Snippet to Load Your Data**
You do not need to write complex code. I have prepared two snippets. Copy-paste the one you need into a new cell.

You only need to change the `REPO_PATH` and `FILE_PATH` variables.

---

### **L2 SNIPPET (for CSV files):**

```javascript
// Copy-paste this entire cell to load a private CSV file

myPrivateCSV = {
  // --- Required ---
  // 1. Get the token you stored in Secrets
  const token = await Secrets.get("GITHUB_TOKEN");

  // 2. Define your repository and file path
  const REPO_PATH = "CDT-ORG/MY-PRIVATE-ASSIGNMENT-REPO"; // e.g., "CDT-ORG/workshop-1-data"
  const FILE_PATH = "data/my_assignment_data.csv"; // e.g., "students/my_data.csv"

  // --- RSE Magic (No need to change) ---
  const url = `https://api.github.com/repos/${REPO_PATH}/contents/${FILE_PATH}`;

  const response = await fetch(url, {
    headers: {
      Accept: "application/vnd.github.v3.raw",
      Authorization: `token ${token}`
    }
  });

  if (!response.ok) throw new Error(`Fetch failed: ${response.status}`);

  const text = await response.text();
  return d3.csvParse(text); // Uses d3's parser
}
```

---

### **L2 SNIPPET (for JSON files):**

```javascript
// Copy-paste this entire cell to load a private JSON file

myPrivateJSON = {
  // --- Required ---
  // 1. Get the token you stored in Secrets
  const token = await Secrets.get("GITHUB_TOKEN");

  // 2. Define your repository and file path
  const REPO_PATH = "CDT-ORG/MY-PRIVATE-ASSIGNMENT-REPO"; // e.g., "CDT-ORG/workshop-1-data"
  const FILE_PATH = "data/my_assignment_data.json"; // e.g., "students/my_data.json"

  // --- RSE Magic (No need to change) ---
  const url = `https://api.github.com/repos/${REPO_PATH}/contents/${FILE_PATH}`;

  const response = await fetch(url, {
    headers: {
      Accept: "application/vnd.github.v3.raw", // We get raw text first
      Authorization: `token ${token}`
    }
  });

  if (!response.ok) throw new Error(`Fetch failed: ${response.status}`);

  const text = await response.text();
  return JSON.parse(text); // Uses JavaScript's built-in parser
}
```

---

<!-- The simplest and most secure way to do this is to **upload the file directly to your private Observable notebook**.

### Step-by-Step Guide (L2)

**Step 1: Set Your Notebook's Visibility**

1.  Before you start, make sure your Observable notebook is **private**.
2.  Click the "Share" button at the top of your notebook.
3.  Ensure the notebook is **not** public. Share it only with your private CDT team (e.g., `@cdt-cohort-1`).
    - **Security:** When your notebook is private, any files you upload to it are _also_ private and can only be accessed by people you share the notebook with.

**Step 2: Upload Your File**

1.  In a new cell, click the **plus icon `(+)`** on the left side and select **"File Attachment"**.

2.  An upload box will appear. Click **"Upload"** and select your internal file (e.g., `my_assignment_data.json`).

3.  Observable will upload the file and automatically write the code for you. It will look like this: -->

<!-- end list -->

<!-- ```javascript
// Observable auto-generates this code for you:
myInternalData = FileAttachment("my_assignment_data.json").json();
```

That's it\! The data is now securely attached to your notebook. -->

---

## Quick Comparison

| Feature         | Method 1: L1 (Public)                | Method 2: L2 (Internal)                                 |
| :-------------- | :----------------------------------- | :------------------------------------------------------ |
| **Data Source** | Public GitHub Repo (Raw URL)         | Private GitHub Repo (API Token)                         |
| **Use Case**    | Public demos, open data              | Assignments, drafts, cohort-only                        |
| **Security**    | Open to the world                    | **Private by default.** Enforced by Observable Secrets. |
| **Code**        | `FileAttachment("http://...").csv()` | RSE-provided `fetch` snippet + a `GITHUB_TOKEN` Secret. |
