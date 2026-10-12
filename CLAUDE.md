# foothill_communications — Foothill YSA (FYSAA) ward texting + announcements

Static site on GitHub Pages (repo root, no build step; https://laiwong22.github.io/foothill_communications/) backed by **Supabase** (Postgres,
email-link logins, Deno edge functions) and **Twilio** (SMS). Setup steps: `SETUP.md`.

- Public: `index.html` (Announcements / Calendar / Get texts) and `u.html?t=TOKEN` (manage/unsubscribe).
  Admin console: `admin/` (roles `owner` > `admin` > `member`; members can sign in but see nothing private).
- `config.js` holds only public values (Supabase URL + anon key, Twilio number). Empty → demo mode with
  sample data from `assets/common.js`. **Never commit secrets**; they live in Supabase Edge Function secrets.
- **Phone numbers must never be stored in the repo** (it's public). They live only in the `subscribers` table.
- Schema: `supabase/migrations/*.sql` (add a new migration file for changes; don't edit the applied one).
  Functions: `subscribe` (web form, double opt-in), `manage` (personal link), `twilio-inbound`
  (JOIN / group keywords / LEAVE x / NAME x / STOP / START, else → Inbox), `dispatch` (sends now +
  scheduled via `supabase/cron.sql`), `reply` (Inbox replies).
- Announcements (texts) and calendar events are separate tables; adding an event never texts anyone.
- Checks after editing: `node --check assets/*.js config.js` and
  `cd supabase/functions && deno check */index.ts` (install deno with `npm i -g deno` if missing).
- Deploy functions: `npx supabase link --project-ref <ref> && npx supabase functions deploy`
  (needs `SUPABASE_ACCESS_TOKEN` in the environment).
