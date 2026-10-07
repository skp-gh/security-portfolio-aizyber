# Publishing setup (one time, about 20 minutes)

## 1. Create the two repos on GitHub

1. Sign in at github.com and click **New repository**.
2. Name it `security-portfolio-aizyber`, set it to **Public**, and do **not** add a README (this folder already has one).
3. Create a second public repo named exactly your GitHub username (for example `santosh-xyz`). GitHub shows its README at the top of your profile.

## 2. Push this folder

In Terminal, from this folder:

```bash
git remote add origin https://github.com/skp-gh/security-portfolio-aizyber.git
git branch -M main
git push -u origin main
```

(The folder is already a git repo with a first commit. If `git push` asks for a password, use a GitHub personal access token, or install GitHub Desktop and choose **Add existing repository**.)

## 3. Set up the profile README

Copy `_profile-readme/README.md` into the username repo you created in step 1 (you can paste it directly in the GitHub web editor). Replace `YOUR-HANDLE` with your LinkedIn handle.

## 4. Turn on GitHub Pages

In `security-portfolio-aizyber`: **Settings > Pages > Source: Deploy from a branch > main / (root) > Save.**
After a minute your site is live at `https://skp-gh.github.io/security-portfolio-aizyber/`. That is your `PORTFOLIO_URL` for LinkedIn posts. Artifact links work without the `.md` extension.

## 5. Find and replace placeholders

- `skp-gh`: your GitHub username
- `YOUR-HANDLE`: your LinkedIn profile handle
- `[PORTFOLIO_URL]`: in each `linkedin/week-NN.md` file

## 6. Polish your profiles

- GitHub: **pin** `security-portfolio-aizyber` on your profile.
- LinkedIn: add the portfolio URL to your **Featured** section and to the website field in your contact info.

## Weekly routine

| When | What |
|---|---|
| Thursday | Weekly drafting task writes the next artifact and post as drafts. Review and edit. |
| Friday to Monday | Finalize, commit, and push. |
| Tuesday or Wednesday, 8 to 10am PT | Publish the LinkedIn post. Put the link in the first comment. |
| A week later | Fill in the results table in that week's `linkedin/` file. |
