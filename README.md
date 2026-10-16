# EverydayFix AI

A React + Node/Express app for low-risk household mishaps. It accepts a text description and optional photo, asks follow-up questions, returns structured advice, redirects high-risk situations, adapts to outcomes, and saves fixes in the browser.

## Features
- Responsive React UI with category discovery, quick-start examples, material selector, preference controls and photo upload.
- FixPath shows reported details, image observations when supplied by the provider, assumptions, safety-gate result and next-action criteria.
- Structured LiveFix plans with materials, ordered and trackable steps, precautions, stop criteria, success criteria and follow-up questions.
- Optional Groq chat-completions API for AI reasoning and vision-capable models.
- Backend intent routing distinguishes greetings, supported issues, clarification, out-of-scope requests, invalid input, and safety-critical reports before solution generation.
- Stain and upholstery requests use a backend clarification gate for exact material, stain source, previous treatment, and relevant care instructions. Uncertain cases receive no material-specific treatment steps.
- Clearly labelled deterministic demo mode if no Groq API key is configured. It has no generic fallback fix; photos are not represented as analysed in this mode.
- ClarifyIQ follow-ups and FixLoop success, partial, no-improvement, stuck, failed and worsened outcomes carried into the next request.
- FixSafe deterministic text rules run before a separate constrained AI safety screen for configured AI requests. A failed AI safety screen blocks routine recommendations.
- My Fix Journal stores fix details, outcomes and step progress in this browser's local storage.
- Upload size, declared MIME type, and image signature validation (JPG, PNG, WEBP; max 8 MB).

## Requirements
- Node.js 18+ and npm.
- Optional Groq API key. The default `qwen/qwen3.8-27b` model supports text and image input according to Groq's vision documentation; availability depends on the Groq account.

## Run locally
1. Unzip the project and open a terminal in `everydayfix-ai`.
2. Install packages:
   ```bash
   npm install
   ```
3. For Groq text intelligence and photo analysis, copy `.env.example` to `.env` and set `GROQ_API_KEY`. `GROQ_MODEL` defaults to `qwen/qwen3.8-27b`.
   - Requests use Groq's official OpenAI-compatible endpoint, `https://api.groq.com/openai/v1/chat/completions`.
   - Groq documents `qwen/qwen3.8-27b` as a model supporting text and image input. Confirm the model is enabled for your own account. If Groq rejects the model or request, the backend reports the provider error and does not claim analysis.
   - The uploaded image is sent as a base64 data URL in Groq's documented `image_url` message format for intent classification, independent safety screening, and solution analysis.
   - Never put the API key in React/Vite environment variables or commit `.env` to GitHub.
   - Never put the API key in React/Vite environment variables or commit `.env` to GitHub.
4. Start both servers:
   ```bash
   npm start
   ```
5. Open `http://localhost:3000`. The API runs at `http://localhost:3001`.

Run the API integration tests and production build with:
```bash
npm test
npm run build
```

If you prefer two terminals:
- Terminal 1: `npm run server`
- Terminal 2: `npm run dev`

## Demo without an API key
The app runs in local demo mode and uses deterministic examples for oil on fabric, wax on a table, mud on shoes, and drink spills on fabric. Demo input routing handles greetings, vague requests, unsupported requests, and unintelligible text without generating a plan. Other understandable practical problems receive clarification rather than a generic solution. Without a configured provider, image requests are rejected because the demo cannot independently assess image safety or provide vision analysis.

To enable photo analysis, set `GROQ_API_KEY` in the server `.env` file, restart the backend, and submit an image with its relevant practical context. The default model is Groq-documented `qwen/qwen3.8-27b`; check account access in your Groq Console. The app only labels an image analysed after Groq processes the request. **Real-provider image analysis has not been verified in this environment because no API key is configured.**

## Production deployment
- Deploy the Express API and Vite frontend as separate services (or route `/api` to Express on the same origin). The API reads `PORT`, `FRONTEND_ORIGIN`, `GROQ_API_KEY`, and `GROQ_MODEL`; set provider credentials only in the API service's server-side environment.
- For a separate frontend host, set build-time `VITE_API_BASE_URL` to the deployed API origin (for example `https://api.example.com`, without a trailing slash). If hosting both behind one origin, leave it empty and route `/api` to Express. The Vite localhost proxy is development-only.
- Set `FRONTEND_ORIGIN` on the API service to the exact deployed frontend origin to restrict CORS (for local development, use `http://localhost:3000`).
- `.env` and other `.env.*` files are ignored by Git; `.env.example` is the checked-in template. Never publish credentials.
- No GitHub remote or hosting provider is configured in this project folder. To publish: create a public GitHub repository, then run `git init`, `git add .`, `git commit -m "Implement stain clarification workflow"`, `git branch -M main`, `git remote add origin <repository-url>`, and `git push -u origin main`. Before `git add`, confirm no real `.env` or credentials are staged.
- For deployment, connect the repository to your chosen frontend/API host, configure the environment variables above in its dashboard, set the frontend build command to `npm run build`, the frontend output directory to `dist`, and the API start command to `npm run server`. Use `https://<api-host>` for `VITE_API_BASE_URL` on the frontend.

## Safety and limitations
- This is a prototype, not an emergency service or professional cleaning guarantee.
- Server-side text pattern checks and the independent AI safety assessment are limited safeguards and cannot identify every hazardous situation. AI safety-screen errors fail closed and return no routine recommendation. Demo safety checks are deterministic and limited. Do not use the app to assess emergencies.
- AI advice can be wrong. Follow product labels, material-care instructions and qualified professional advice.
- Do not submit sensitive images or personal information. Uploaded images are sent to the configured AI provider only when an API key is configured and the request is made; the provider receives an independent safety-screen request before recommendation generation.
- This build is for low-risk household issues; active fire, suspected gas leaks, electrical danger, medical emergencies and structural hazards should be redirected to appropriate local services.

## Suggested 60-second demo
1. Choose “Oil on fabric”, select “Cotton or washable fabric”, and submit.
2. Show the structured plan, materials, steps, avoid list and uncertainty.
3. Answer a ClarifyIQ question and see your update included in the decision path.
4. Mark steps as complete, then use “I’m stuck” or “This made things worse” to request an adapted plan.
5. Enter a dangerous example such as “I mixed bleach and ammonia” to show the safety redirect.
6. Reopen or delete a saved fix; configure a vision-capable AI model to enable photo analysis.
