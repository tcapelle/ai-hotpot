---
name: ai-lesson-for-kids
description: >
  Run an interactive AI lesson for kids age 7-10 in a classroom setting. The teacher
  collaborates live with Claude Code: updating a kid-friendly website in real-time,
  generating AI images from kids' handwritten prompts using Gemini, and embedding a
  Teachable Machine webcam classifier.
---

# AI Lesson for Kids (Age 7-10)

Interactive ~45-minute AI lesson where kids learn what AI is, train their own image classifier,
and generate AI art — all appearing live on a class website.

## Prerequisites

- **API Keys** (set as environment variables):
  - `NETLIFY_AUTH_TOKEN` — for deploying the website
  - `GEMINI_API_KEY` — for AI image generation
- **Tools:**
  - [Claude Code](https://docs.anthropic.com/en/docs/claude-code) v2.1.51+ (logged in via `claude /login`)
  - [Netlify CLI](https://docs.netlify.com/cli/get-started/) (`npm install -g netlify-cli`)
  - `curl`, `python3`, `bash`
- **Hardware:**
  - Computer with webcam + projector for Teachable Machine demo
  - Phone for remote-controlling Claude Code during class
- **Printed materials:**
  - [CS Unplugged "Intelligent Paper"](https://www.cs4fn.org/teachers/activities/intelligentpaper/intelligentpaper.pdf) tic-tac-toe sheet
  - QR code for the site URL (so kids can show parents at home)

## Quick Setup

### 1. Create a deploy directory and customize the template

```bash
mkdir -p /tmp/ai-lesson-site

# Copy the HTML template
cp assets/template.html /tmp/ai-lesson-site/index.html

# Edit the <title> and <h1> to match your class name
# Replace "NAZWA KLASY" with your class name
```

### 2. Deploy to Netlify

```bash
cd /tmp/ai-lesson-site
npx netlify deploy --dir=. --prod
# Note the site URL and site ID for later deploys
```

### 3. Prepare "What to Watch Out For" images

Generate 3 images based on metaphors kids already know:

```bash
./scripts/generate_image.sh "Pinocchio with a long nose, lying, cartoon style" /tmp/ai-lesson-site/uwaga1.jpg
./scripts/generate_image.sh "the Mirror of Erised from Harry Potter showing someone what they want to see" /tmp/ai-lesson-site/uwaga2.jpg
./scripts/generate_image.sh "a boy in a wheelchair from The Secret Garden, his legs weak from not using them" /tmp/ai-lesson-site/uwaga3.jpg
```

These represent: AI hallucination (Pinocchio), AI sycophancy (Mirror of Erised), and cognitive atrophy from over-reliance on AI (The Secret Garden).

### 4. Redeploy with images and print QR code

```bash
cd /tmp/ai-lesson-site
npx netlify deploy --site YOUR_SITE_ID --dir=. --prod
```

Generate a QR code for the site URL and print it — kids take it home with the tic-tac-toe handout.

## During the Lesson

The teacher controls the lesson **from their phone** using Claude Code's Remote Control feature.
Claude Code runs on the laptop (connected to the projector), and the teacher sends instructions
from their phone — Claude edits the HTML and redeploys automatically in ~5 seconds.

### Start Remote Control (before class)

```bash
cd /path/to/ai-lesson-for-kids
claude remote-control --name "AI Lesson"
```

A QR code appears in the terminal. Scan it with your phone → opens at claude.ai/code,
connected to your running session. You now control Claude from your phone while walking
around the classroom.

### Update a section

From your phone, type:

> Update section 1 with: AI jest wtedy, gdy komputer wykonuje sprytne reguły

Claude edits the HTML and redeploys. Kids refresh to see changes.

### Generate image from a kid's prompt

From your phone, type:

> Generate an image of a flying cat riding a rainbow and add it to the gallery as "Zosia's prompt"

Claude runs the Gemini script, adds the `<img>` tag, and redeploys.

### Embed Teachable Machine model

1. During class, train a model at https://teachablemachine.withgoogle.com/train/image
2. Export → Upload → copy the model URL
3. From your phone, type:

> Uncomment the Teachable Machine section and set the model URL to https://teachablemachine.withgoogle.com/models/abc123/

Claude uncomments the HTML/JS, sets the URL, and redeploys.

## Lesson Plan

See [references/lesson-plan.md](references/lesson-plan.md) for the full 6-section lesson plan
with timing (~45 min), tips, and preparation checklist.

## Template

The HTML template is at [assets/template.html](assets/template.html). It includes:
- 6 sections with Polish placeholder text (easily translatable)
- Teachable Machine integration (commented out, ready to activate)
- AI image gallery
- "Na co uważać" (What to Watch Out For) section with 3 image slots
- Responsive, kid-friendly purple gradient design

## Image Generation Script

[scripts/generate_image.sh](scripts/generate_image.sh) generates images via the Gemini API.
Automatically appends "kid-friendly, colorful, cartoon style" to all prompts.

Usage: `./scripts/generate_image.sh "prompt text" output_filename.png`

Requires: `GEMINI_API_KEY` environment variable.

## Tips

- Kids love seeing their words appear on a "real website" — the live updates are engaging
- 30-50 webcam samples per Teachable Machine class is enough
- Let kids write prompts on paper → photograph → transcribe (more fun than typing)
- Rock-Paper-Scissors works great as a Teachable Machine demo (everyone has hands!)
- The "Intelligent Paper" tic-tac-toe hook from [CS Unplugged](https://classic.csunplugged.org/artificial-intelligence/) grabs attention immediately
- Budget at least 15 minutes for the gallery — it's the highlight
- Have a kid be your assistant and follow the paper algorithm (makes it more fun)

## Using with Claude Code

This skill is designed to work with [Claude Code](https://docs.anthropic.com/en/docs/claude-code)
and its **Remote Control** feature. Claude Code runs on your laptop and handles all file editing,
image generation, and deployments — it acts as invisible infrastructure. The kids never see it,
they just see the website updating.

### Before the lesson

1. Set `NETLIFY_AUTH_TOKEN` and `GEMINI_API_KEY` in your `.env`
2. Open a terminal in this skill's directory
3. Start remote control:
   ```bash
   claude remote-control --name "AI Lesson"
   ```
4. Scan the QR code with your phone — you're connected

### During the lesson

Type instructions from your phone. Claude handles everything on the laptop:

- "Update section 1 with: AI is when a computer follows smart rules to solve problems"
- "Generate an image of a flying cat riding a rainbow and add it to the gallery as 'Zosia's prompt'"
- "Uncomment the Teachable Machine section and set the model URL to https://teachablemachine.withgoogle.com/models/abc123/"
- "Redeploy the site"

### Requirements

- Claude Code v2.1.51+
- Logged in via `claude /login` (claude.ai account, not API key)
- Phone with a browser (no app install needed)

The lesson also works without Claude Code — just edit HTML manually and deploy.
