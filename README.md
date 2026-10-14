# BloodLink: Automated Blood Donation Information Portal with CI/CD

BloodLink is a **static blood donation information website** packaged with Docker and managed through a Jenkins CI/CD pipeline.

The website provides general blood donation information. It does **not** register donors, match patients with donors, maintain blood-bank inventory, or arrange donations.

## 1. Technology stack

- **HTML and CSS** — website structure and styling
- **Git and GitHub** — version control and remote source repository
- **Docker** — packages and runs the website
- **NGINX** — serves the static website
- **Jenkins** — automates verification, image building, testing, and deployment

## 2. Start the project on your Mac

Do these steps whenever you want to demonstrate the project again.

### Step A: Start Docker Desktop

1. Open **Docker Desktop** from Applications.
2. Wait until Docker says it is running.
3. Open Terminal and check Docker:

```bash
docker ps
```

### Step B: Start Jenkins

In Terminal, run:

```bash
brew services start jenkins-lts
```

If Jenkins is already running, this may say the service is already started. That is fine.

Open Jenkins in your browser:

<http://localhost:8080>

Sign in with your existing Jenkins account.

### Step C: Open the BloodLink website

The deployed website normally runs at:

<http://localhost:8081>

If the page loads, show the homepage. To verify the HTTP response from Terminal, run:

```bash
curl -I http://localhost:8081
```

A response containing `HTTP/1.1 200 OK` means the web server is responding successfully.

Check that the container is running:

```bash
docker ps
```

Look for the container named `bloodlink-app` and the port mapping `8081->80`.

## 3. Run the CI/CD pipeline

1. Open <http://localhost:8080>.
2. Open the Jenkins job **BloodLink-CI-CD**.
3. Click **Build Now** when you want to run the pipeline manually.
4. Open the new build number.
5. Click **Console Output** to see the stages and result.

The job is configured with **Poll SCM** using this schedule:

```text
H/2 * * * *
```

Jenkins checks the GitHub repository periodically. When it detects a new commit, it can start the pipeline automatically. Automatic polling requires Jenkins to be running and able to reach GitHub.

Do not click **Build Now** when you are specifically demonstrating automatic polling. Instead, push a real commit to GitHub, wait a few minutes, and check whether Jenkins starts a build by itself.

## 4. What each pipeline stage does

1. **Checkout** — Jenkins obtains the project from GitHub through the job's Pipeline-from-SCM configuration.
2. **Verify Files** — checks that `index.html`, `styles.css`, and `Dockerfile` exist.
3. **Build Docker Image** — builds the image named `bloodlink:1.0`.
4. **Test Website** — runs `nginx -t` inside the image to test the NGINX configuration.
5. **Deploy** — removes the existing container named `bloodlink-app`, starts a new container from `bloodlink:1.0` on port 8081, and checks the website with `curl`.

A successful build should finish with `Finished: SUCCESS`. If a stage fails, open that build's **Console Output** and read the error before retrying.

## 5. Demonstrate the project

Use this order for a short demonstration:

1. **Website:** open `http://localhost:8081` and explain that it is an informational blood donation portal.
2. **GitHub:** open the repository and show `index.html`, `styles.css`, `Dockerfile`, and `Jenkinsfile`.
3. **Docker:** run `docker ps` and show `bloodlink-app` with port `8081->80`.
4. **Jenkins pipeline:** open `BloodLink-CI-CD`, open a successful build, and show the stages and Console Output.
5. **Automatic polling:** show the Jenkins job's Poll SCM configuration and the Git Polling Log showing `Changes found`. Explain that Jenkins detected a pushed commit.
6. **Deployment verification:** run `curl -I http://localhost:8081` and show `HTTP/1.1 200 OK`.

## 6. Useful commands

Run these in **Mac Terminal**, not in the Jenkins browser.

Check Docker containers:

```bash
docker ps
```

Check website response:

```bash
curl -I http://localhost:8081
```

Go to the project folder:

```bash
cd ~/BloodLink
```

Check Git status:

```bash
git status
```

See recent commits:

```bash
git log --oneline -5
```

Push a code change after editing a file:

```bash
git add .
git commit -m "Describe the change"
git push origin main
```

Only use the Git commands when you have actually changed project files. Do not make empty or unnecessary commits just to trigger Jenkins.

## 7. Troubleshooting

### Website does not open on port 8081

Check Docker Desktop is running, then run:

```bash
docker ps
```

If `bloodlink-app` is not running, run the Jenkins pipeline from the **BloodLink-CI-CD** job. The pipeline should build the image and deploy the container. Then retry:

```bash
curl -I http://localhost:8081
```

### Jenkins does not open on port 8080

Run:

```bash
brew services list
```

If `jenkins-lts` is stopped, run:

```bash
brew services start jenkins-lts
```

Then try <http://localhost:8080> again.

### Automatic polling does not trigger a build

- Confirm Jenkins is running.
- Confirm **Poll SCM** is checked in the job configuration.
- Confirm the schedule is `H/2 * * * *`.
- Confirm the commit was pushed to the repository's `main` branch.
- Open **Git Polling Log** and look for `Changes found`.
- Allow a few minutes for the next poll.

### Docker permission or connection error in Jenkins

Make sure Docker Desktop is fully running. Review the failing build's **Console Output**. Do not assume the deployment succeeded unless the build log and website check confirm it.

## 8. Important project scope and limitations

- The project has been demonstrated locally on macOS; it is not a public production deployment.
- The website is informational and does not provide donor registration, live donor matching, or blood-bank inventory.
- The pipeline verifies required files, builds a Docker image, checks NGINX configuration, and checks that the deployed website responds. It is not a comprehensive functional or security test suite.
- Poll SCM checks periodically; it is not an instant GitHub webhook.

## 9. Stop services when finished

You can close the browser when you finish demonstrating the project. To stop Jenkins, run:

```bash
brew services stop jenkins-lts
```

To stop the website container without deleting it, run:

```bash
docker stop bloodlink-app
```

Start Docker Desktop and Jenkins again next time, then run the pipeline if you need to redeploy the website.
