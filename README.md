# Project Deployment and Workflow Guide

This guide explains the step-by-step workflow for updating, testing, and deploying this MkDocs documentation site. 

## Workflow Overview

Whenever you make updates to the documentation, follow this order:
1. **Update**: Make your necessary changes to the markdown files in the `docs` folder.
2. **Serve Locally**: Check locally to ensure your changes look correct and nothing is broken.
3. **Push to GitHub**: Commit and push your changes.
4. **Deploy**: Deployment happens automatically via GitHub Actions, with a manual fallback option.

## Prerequisites

If you haven't already, ensure you have installed the required Python packages for MkDocs:
```powershell
pip install -r requirements.txt
```

---

## Step-by-Step Commands (PowerShell)

### 1. Serve Locally (Testing)
Before pushing any changes, serve the documentation locally to check if everything is working as expected. Run this command in PowerShell:

```powershell
python -m mkdocs serve
```
Open your browser and navigate to `http://127.0.0.1:8000` to preview your changes.

### 2. Push to GitHub
Once you are satisfied with the local preview, you can push the changes to GitHub. Press `Ctrl + C` in PowerShell to stop the local server, then run the following Git commands:

```powershell
git add .
git commit -m "Describe your updates here"
git push
```

### 3. Hosting & Deployment

**Automatic Deployment (Primary)**
Hosting and deployment are handled automatically through GitHub Actions. Once you push your changes to the `main` branch, the GitHub Action will trigger and deploy the latest version of the site automatically.

**Manual Deployment (Optional)**
If you need to deploy the site manually from your local machine, you can use the `gh-deploy` command. This will build the site and push it to the `gh-pages` branch:

```powershell
python -m mkdocs gh-deploy
```

---

## Important Repositories

- **Python Handbook Repository:** [https://github.com/pyt702/Python_HandBook](https://github.com/pyt702/Python_HandBook) (This project)
- **Climato Repository:** [https://github.com/pyt702/Climato](https://github.com/pyt702/Climato) (The capstone project mentioned in Chapter 6)
