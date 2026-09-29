# TubeDown

TubeDown is a Next.js 14 frontend and Express API for retrieving public YouTube video metadata and streaming selected MP4 or MP3 formats. The API uses `yt-dlp` through `youtube-dl-exec`; MP3 output is transcoded with the bundled ffmpeg binary.

Use this only for videos you own or are authorized to download. Respect YouTube's Terms of Service and applicable copyright law. YouTube may change or restrict its delivery endpoints, and age-restricted, private, or otherwise unavailable videos cannot be processed. This project does not bypass sign-in, DRM, or other access controls.

## Requirements

- Node.js 20 or newer
- npm 10 or newer
- Python 3.9 or newer available as `python3` during `youtube-dl-exec` installation; the package bundles the yt-dlp executable

## Local setup

1. From the repository root, install all workspace dependencies:

   ```bash
   npm install
   ```

2. Create `backend/.env` from `backend/.env.example`. Set `PORT=5000` and `FRONTEND_URL=http://localhost:3000` for local development.

3. Create `frontend/.env.local` with:

   ```env
   NEXT_PUBLIC_API_URL=http://localhost:5000/api
   ```

4. Start the backend and frontend together from the repository root:

   ```bash
   npm run dev
   ```

5. Open <http://localhost:3000>. The backend health endpoint is <http://localhost:5000/health>.

## Production builds

```bash
npm run typecheck:backend
npm run typecheck:frontend
npm run build:backend
npm run build:frontend
```

## Deployment

### Frontend on Vercel

Import the repository into Vercel and set **Root Directory** to `frontend`. Configure `NEXT_PUBLIC_API_URL` to the deployed API origin followed by `/api` (for example, `https://your-api.onrender.com/api`), then deploy with the default Next.js build settings.

### Backend on Render

The included `render.yaml` creates the Node web service from `backend/`. Set `FRONTEND_URL` to the deployed Vercel origin in the Render service environment. Render supplies `PORT` automatically. The service exposes `/health` and `/api/video`.

The backend applies per-IP rate limits to metadata lookups and downloads. If the frontend uses a custom domain or multiple origins, update the CORS allowlist in `backend/src/server.ts` before deployment.

## API

- `POST /api/video/info` with JSON `{ "url": "https://www.youtube.com/watch?v=..." }` returns title, thumbnail, duration, author, compact view count, and available MP4/MP3 formats.
- `GET /api/video/download?url=...&itag=...` streams the selected file as an attachment. Use `itag=-1` for MP3.

The API accepts standard YouTube watch, short, live, embed, and `youtu.be` video links. It rejects non-YouTube hosts and malformed video IDs.
