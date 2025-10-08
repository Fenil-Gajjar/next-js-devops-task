# Project Setup & Local Run

This step guides you through setting up the project locally on your Ubuntu VM, installing dependencies, running the Next.js application, and verifying it works before Dockerization.

## 1. Clone the Repository

First, clone the project repository from GitHub to your local Ubuntu VM:

```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>
```

*   `git clone`: Downloads the repository to your local machine.
*   `cd <repo-name>`: Changes directory into the project folder.
*   **Why**: This gives you the full source code, Dockerfile, Kubernetes manifests, and GitHub Actions workflow.

## 2. Install Node.js Dependencies

Next, install all project dependencies using npm:

```bash
npm install
```

*   **What it does**:
    *   Reads `package.json` and installs all required packages for Next.js (React, Webpack, etc.)
    *   Creates a `node_modules` folder with all dependencies.
*   **Why**: The Next.js app cannot run without its dependencies.
*   **Verification**:

    ```bash
    ls node_modules
    ```

    You should see directories like `react`, `next`, `webpack`, etc.

## 3. Run Next.js Locally (Development Mode)

Start the development server:

```bash
npm run dev
```

*   **What it does**:
    *   Starts a Node.js server running your Next.js application on port 3000.
    *   Enables hot-reloading: changes in the code automatically update the browser.
*   **Verification**:

    Open a browser inside the Ubuntu VM: `http://localhost:3000`

    You should see the Next.js application home page.

## 4. Build Next.js for Production

Before deploying via Docker, build the production-ready app:

```bash
npm run build
```

*   **What it does**:
    *   Compiles and optimizes the Next.js app for production
    *   Outputs static assets and server code to `.next` folder
*   **Why**: Docker container will serve the optimized production version, not the development server.

## 5. Run Production Version Locally (Optional)

After building, test the production build:

```bash
npm run start
```

*   **What it does**:
    *   Starts the server in production mode
    *   Runs optimized code from `.next` folder
*   **Verification**:

    Open browser: `http://localhost:3000`

    Application should behave identically to development mode but optimized.

## 6. Key Notes

*   **Port 3000**: The application runs on port 3000 by default. You can change this by setting `PORT` environment variable.
*   **Why local run is important**:
    *   Confirms the application works before containerizing
    *   Helps catch any runtime or dependency issues early

## Outcome of this step

*   Project source code is cloned and accessible
*   All Node.js dependencies are installed
*   Next.js app runs successfully in both development and production modes
*   Ready for Dockerization in the next step

