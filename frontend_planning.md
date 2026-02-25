# Frontend Planning (Phase 6)

## Next Steps for the UI (Internal & Contractor Portals)

According to the V3 Master Prompt, Phase 4 Stylize includes:
> "UI/UX: If the project includes a dashboard or frontend, apply clean CSS/HTML and intuitive layouts."

And under Internal Portal:
> Dashboard: Total opportunities, HOT matches, Upcoming deadlines, High maturity score ops.
> Buttons: [Backfill 90 Days], [Sync Last 2 Days], [Recalculate Scores], [Generate Intelligence Report], [Simulate Weighting], [Export CSV], [Archive Opportunity].

To achieve this deterministically without mixing Python scripts directly into React:

1.  **Frontend Framework:** Next.js 14 (App Router) using Tailwind CSS for premium, fast styling.
2.  **State Management & Data:** Supabase Client for React (`@supabase/ssr` or `@supabase/supabase-js`) to directly query the `matches`, `opportunities`, and `agency_intelligence_logs` tables. This consumes the JSON schemas defined in `architecture/4_ui_payload_schema.md`.
3.  **Action Buttons (The Triggers):** The UI buttons (like `[Sync Last 2 Days]`) must trigger the execution of our Python scripts in `tools/`. Since Next.js runs as a separate Node process, we will need to create either Next.js API Routes that execute bash commands using Node's `child_process`, OR we wrap the Python scripts in a lightweight backend server (like FastAPI), or invoke them via serverless functions (if deployed to Vercel, Supabase Edge Functions or Python Serverless would be needed for production, but locally we can use `child_process`).

## Recommended Local Setup
Since we are building a deterministic local Core OS first:
1. Initialize a Next.js app in a subdirectory (e.g., `dashboard/`).
2. Build the UI views.
3. For the action buttons, we write Next.js API endpoints that use `exec` to run `python tools/1_ingest_sam.py`, etc., capturing standard output to stream back to the UI.
