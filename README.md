# BYOTC slides

Source for the BSidesPDX 2026 talk, **Bring Your Own Trusted Caller (BYOTC): A New Way to Exploit Vulnerable Windows Drivers**. The deck has 56 slides and is written in [Slidev](https://sli.dev/), so content and styling can be reviewed in Git.

The deck introduces BYOVD, develops the Malwarebytes Chameleon example, and presents the AI-assisted research workflow and eight reported drivers. System Informer's protection and evil-parent chain, the VirtualBox process-hardening report, and five shorter caller-trust failure modes follow. The AV enforcement question and Q&A are the final two slides. Source links and evidence boundaries are in the notes in `slides.md`. The VirtualBox material comes from Oracle disclosure S2240975; review its disclosure scope before a public presentation.

## Run locally

```sh
npm ci
npm run dev
```

Open the URL printed by Slidev. The Malwarebytes demonstration video is stored under `public/demos/` for offline playback.

## Build or export

```sh
npm run build
npm run export
```

For a static preview after `npm run build`:

```sh
python3 preview.py
```

Open `http://127.0.0.1:3030/` and rebuild after edits. PDF export requires Chromium available to Playwright.

Edit `slides.md` for content and `style.css` for styling. Source and assets are tracked; dependencies, build output, and exported PDFs are ignored.
