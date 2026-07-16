# FitVerse AI — Frontend Prototype

An interactive prototype of the FitVerse AI fitness/nutrition platform: an
Apple-inspired landing page, a live dashboard, and an AI chat coach.

## Run it

```bash
npm install
npm run dev
```

Open http://localhost:3000. Requires internet access on first run (fonts are
fetched from Google Fonts at build time via `next/font`).

## What's real vs. mocked in this prototype

This is a **frontend-only** prototype — there's no Python backend, database,
or trained ML models here. To keep it honest about what's simulated:

**Genuinely working (real formulas, not fake numbers):**
- Calorie targets — actual Mifflin-St Jeor equation + activity multiplier (`lib/health.ts`)
- BMI classification — standard WHO BMI bands
- Body-fat estimate — U.S. Navy circumference method
- 90-day weight projection — energy-balance model (~7700 kcal/kg) with a
  metabolic-adaptation decay term
- Workout plan — real rule-based matching against a 20-exercise dataset,
  filtered by your equipment/experience/goal (`lib/workouts.ts`)
- Meal plan — cosine-similarity matching against a small food dataset with
  real macros (`lib/meals.ts`)
- Everything recalculates live as you edit the profile form — no page reload,
  no server round-trip.

**Mocked / stubbed (clearly labeled in the UI):**
- The AI Chat Coach uses local keyword rules (`lib/coach.ts`), not a live
  LLM. Swap it for a real call to the Anthropic API or your model of choice —
  the response function's signature (`getCoachResponse(message, context)`) is
  designed to be a drop-in replacement point.
- Food-photo recognition, barcode/OCR scanning, and pose-based form
  correction are described on the landing page as capabilities but are not
  wired up — those need a computer-vision backend (see below).

## Extending toward the full spec

The original brief calls for a FastAPI + MongoDB backend and several trained
ML models (XGBoost calorie model, hybrid recommenders, LSTM progress
predictor, MediaPipe pose detection, EfficientNet/YOLO food recognition,
OCR). None of that can run inside a chat-generated prototype — it needs:

1. A hosted backend (FastAPI on Render/Railway) with MongoDB Atlas.
2. Trained models served via ONNX Runtime or a model API, with real datasets
   (USDA FoodData Central, Kaggle Gym Exercise Dataset, Food-101, Fitbit
   Fitness Tracker Dataset) replacing the hand-written datasets in `lib/`.
3. Auth (JWT + OAuth) and a `users`/`progress_logs` collection so the
   dashboard reads/writes real per-user history instead of recomputing from
   form inputs each render.

This prototype's job is to prove out the product experience and interaction
model so that backend work has a concrete target to build against.

## Structure

```
app/
  page.tsx            landing page
  dashboard/page.tsx   live calculators, charts, workout & meal plans
  coach/page.tsx       chat coach
components/            UI components (Navbar, VitalRing, GlassCard, etc.)
lib/                    calorie/BMI/body-fat math, workout & meal engines, coach rules
```
