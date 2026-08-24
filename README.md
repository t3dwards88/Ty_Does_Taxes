[README.md](https://github.com/user-attachments/files/31383119/README.md)
# Putting your calculator on the internet — the simple version

This is a step-by-step guide written for someone who has never done this before.
Take it one step at a time. Don't skip ahead.

Total cost: **$0 to start** (domain costs about $12-15/year once you buy that part).
Total time: **about 30-45 minutes** the first time.

---

## Step 1: Get a free GitHub account

GitHub is just a place on the internet to store your code. Think of it like Google Drive, but for code.

1. Go to https://github.com
2. Click "Sign up"
3. Use your email, pick a username, make a password
4. Verify your email when it asks

You're done with this step once you can log in.

---

## Step 2: Put your project on GitHub

1. Once logged into GitHub, click the **+** icon in the top right, then **"New repository"**
2. Name it `ty-does-taxes` (or anything you want)
3. Leave everything else as default
4. Click **"Create repository"**
5. GitHub will show you a page with some commands. Ignore all that — instead, look for a link that says **"uploading an existing file"**
6. Drag and drop every file in this project folder into that upload box (all the files I gave you: `src` folder, `package.json`, `index.html`, everything)
7. Scroll down, click **"Commit changes"**

Your code is now on the internet, but not "live" as a website yet. That's the next step.

---

## Step 3: Make it a real live website with Vercel

Vercel is a free service that takes your code from GitHub and turns it into a real website with a real link.

1. Go to https://vercel.com
2. Click **"Sign up"**
3. Choose **"Continue with GitHub"** — this connects the two for you automatically
4. Once logged in, click **"Add New Project"**
5. You'll see the `ty-does-taxes` repository you made — click **"Import"** next to it
6. Vercel will auto-detect it's a Vite project. You shouldn't need to change any settings.
7. Click **"Deploy"**
8. Wait about 60-90 seconds. A little confetti animation means it worked.
9. Vercel gives you a free link that looks like `ty-does-taxes.vercel.app` — click it. Your calculator is now live on the internet, for real, right now.

**Bookmark your Vercel dashboard.** Any time you want to update the calculator later, you just update the files on GitHub and Vercel automatically rebuilds the site within a minute. You never have to redo Steps 1-3 again.

---

## Step 4: Buy your domain (tydoestaxes.com)

1. Go to a domain registrar — **Namecheap** (namecheap.com) is a good, simple option
2. Search "tydoestaxes.com" in their search bar
3. If it's available, add it to cart and buy it (usually $10-15/year)
4. If it's NOT available, try `.co` or `tydoestaxes.net` as a backup, or a variation like `hiretydoestaxes.com`

---

## Step 5: Point your domain at your Vercel site

This step connects the domain you just bought to the website you built.

1. Go back to your Vercel dashboard, click your project, click **"Settings"**, then **"Domains"**
2. Type in `tydoestaxes.com` and click **"Add"**
3. Vercel will show you 1-2 lines of technical-looking text (called "DNS records"). Don't worry about understanding them — just keep this tab open.
4. Open a new tab, log into Namecheap, find your domain, look for **"Advanced DNS"** or **"Manage DNS"**
5. Copy the values Vercel showed you into Namecheap's DNS settings (Vercel's instructions will tell you exactly which type — usually an "A Record" and a "CNAME Record")
6. Save in Namecheap
7. Wait — this part can take anywhere from 10 minutes to a few hours to "kick in." This is normal, it's not broken, just be patient.
8. Once it's ready, typing `tydoestaxes.com` into any browser will show your calculator.

---

## Step 6: Connect the state request form (so you actually get the requests)

Right now, when someone requests a new state, that request needs somewhere to go. We use a free tool called Formspree for this.

1. Go to https://formspree.io
2. Sign up for a free account
3. Click **"New Form"**, name it anything like "State Requests"
4. It will give you a URL that looks like `https://formspree.io/f/abc123xyz`
5. Copy that URL
6. Go back to your GitHub repository, open the file `src/App.jsx`
7. Find this line near the top of the file (use Ctrl+F / Cmd+F to search for "FORMSPREE"):
   ```
   const FORMSPREE_ENDPOINT = "https://formspree.io/f/YOUR_FORM_ID";
   ```
8. Replace `https://formspree.io/f/YOUR_FORM_ID` with the real URL Formspree gave you
9. Save/commit the change on GitHub (there's a "Commit changes" button right on the file edit page)
10. Vercel will automatically rebuild your site with this change within about a minute

Now, whenever someone requests a state, you'll get an email from Formspree with the details. Free plan covers 50 submissions/month, which is plenty to start.

---

## Step 7: Connect the results/leads form (for the PDF email opt-in)

The results page also lets people leave their email to get a copy of their results or to be contacted about tax prep. This needs its own separate Formspree form so these leads don't mix in with state requests.

1. Go back to https://formspree.io (you're already signed up from Step 6)
2. Click **"New Form"** again, name it something like "Results Leads"
3. Copy the new URL it gives you (looks like `https://formspree.io/f/xyz789abc`)
4. In GitHub, open `src/App.jsx` again
5. Search for `FORMSPREE_LEADS_ENDPOINT` (use Ctrl+F / Cmd+F)
6. Replace `https://formspree.io/f/YOUR_LEADS_FORM_ID` with your new URL
7. Commit the change — Vercel will rebuild automatically

Now, when someone checks a box to get their results emailed or to be contacted about tax prep, you'll get that as a separate email in your Formspree "Results Leads" form.

---

## What to do if something breaks

- **Site shows a blank white page:** go to your Vercel dashboard, click the project, click the latest "Deployment," and look at the build log for a red error message. Paste that error into a new chat with me and I'll help you fix it.
- **Domain isn't working after a few hours:** double check the DNS records in Namecheap match exactly what Vercel asked for.
- **Not receiving state request emails:** double check you pasted your real Formspree URL into `App.jsx` and that the site rebuilt (check the "Deployments" tab in Vercel for a recent green checkmark).

You will not break anything permanently. Everything here can be undone or redone. Go slow, one step at a time, and it'll work.
