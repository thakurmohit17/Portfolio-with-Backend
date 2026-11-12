# Mohit Singh Portfolio

This repository is being migrated in phases from the approved static
`mohit-singh-portfolio.html` design to a reusable, database-driven Next.js
portfolio. The original HTML remains unchanged.

## Phase 1: preserved public frontend

The Next.js App Router frontend is split into reusable section components.
The original stylesheet and embedded portrait have been extracted without
changing the source HTML. Project filters, scroll/reveal effects, count-up
statistics, the experience timeline and a mobile navigation toggle are
implemented.

See [the component map](./docs/portfolio-component-map.md) for the section
inventory and design/interaction notes.

## Supabase setup

The public portfolio reads published content from Supabase using the anon key.
Copy `.env.example` to `.env.local` and set the project URL and anon key:

```env
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
```

Apply `supabase/migrations/20261105000000_initial_portfolio_schema.sql` to the
project, then run `supabase/seed.sql` to load the original portfolio content.
The seed is safe to run again. Row-level security allows public reads of
published, active content and limits writes to authenticated users whose
`app_metadata.role` is `admin`. Keep service-role keys out of browser code,
`.env.local`, and source control.

The frontend loads published section configuration and content at request time.
It reports an explicit configuration or database error if Supabase is missing
or required published content cannot be read. Admin authentication and CRUD
screens are available at `/admin`.

### Admin access

Create or invite an account in **Supabase → Authentication → Users**, then grant
the account an `admin` app-metadata role from the Supabase SQL Editor. Replace
the email with that account's address:

```sql
update auth.users
set raw_app_meta_data =
  coalesce(raw_app_meta_data, '{}'::jsonb) || '{"role":"admin"}'::jsonb
where email = 'your-admin-email@example.com';
```

Sign out and back in after changing the role so Supabase issues a fresh access
token. Open `/admin` to sign in. The initial panel can create, edit, publish,
deactivate, order, and delete projects, experience entries, skill groups, and
skills. Row-level security remains the authorization boundary; never grant
admin access by editing user metadata from the browser.

## Local development

Requirements: Node.js 20.9 or newer and npm.

```bash
npm install
npm run dev
```

Open <http://localhost:3000>. Validate a production build with:

```bash
npm run lint
npm run build
```

## Current scope

The public page, normalized schema, seed data, and published-content reads are
implemented. The admin panel covers the core project, experience, and skills
records. Revision workflows, media upload management, and editing other
portfolio sections remain future work.
