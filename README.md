# VIDMA ONLINE TAKSALAWA — Secure version

## Security fixes in this version

1. **No secret API key is bundled.** `config.js` contains placeholders only. Put only the Supabase **Publishable/anon** key in the browser. Never use `service_role` or `sb_secret_*`. A publishable/anon key is designed for frontend use; RLS is what protects the database.
2. **RLS is enabled on every application table.** Most importantly, students cannot query restricted recordings anymore. They can read only `is_free = true` recordings or recordings assigned to their own account.
3. **Auth request-loop protection.** The app no longer has an `onAuthStateChange -> boot()` feedback listener. Login/logout explicitly control the UI, and `boot()` is guarded against duplicate concurrent execution.
4. **Profile protection.** Normal students cannot change `full_name`, `dob`, or `role` through a crafted client request; admins can.
5. **Restricted recordings are no longer auto-assigned.** Admin must explicitly grant access, while Free recordings remain available to all authenticated students.

## Setup
1. Open `config.js`. Add your Supabase Project URL and **Publishable/anon key**.
2. Run the entire `supabase.sql` in Supabase SQL Editor. It is written to safely drop/recreate the app policies and can be rerun.
3. Register your admin account and set `profiles.role = 'admin'` in Supabase Table Editor.
4. If you previously ran an older SQL version, **run this new SQL file again** so the old recording policy is replaced.

### Important about “API keys on client-side”
For a static HTML/JS site hosted on GitHub Pages, the Supabase Publishable/anon key is normally present in client-side code. That is expected and is not a secret. The secret/service-role key must never be placed there. If you require zero database keys in the browser, the architecture must change to a backend/server-side API.

## Optional Profile Picture
- Students do **not** need to upload a picture during registration.
- After login, open **Profile** → choose an image → **Upload Photo**.
- The image is stored in the Supabase Storage `avatars` bucket and the profile keeps its public image URL.
- The included SQL creates the bucket and Storage policies. Run the included `supabase.sql` once after this update.
- Maximum image size enforced by the app: **5 MB**.
- A **Remove** button is shown after a picture has been uploaded.
