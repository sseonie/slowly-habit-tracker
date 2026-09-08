# Slowly cloud sync setup

The app is already connected to this Supabase project. Complete these one-time dashboard steps before using **Sign in**.

## 1. Create the data table

In Supabase, open **SQL Editor** → **New query**, paste this, then click **Run**:

```sql
create table public.habit_data (
  user_id uuid primary key references auth.users(id) on delete cascade,
  payload jsonb not null default '[]'::jsonb,
  updated_at timestamptz not null default now()
);

alter table public.habit_data enable row level security;

create policy "Users can read their own habits"
on public.habit_data for select to authenticated
using (auth.uid() = user_id);

create policy "Users can insert their own habits"
on public.habit_data for insert to authenticated
with check (auth.uid() = user_id);

create policy "Users can update their own habits"
on public.habit_data for update to authenticated
using (auth.uid() = user_id)
with check (auth.uid() = user_id);
```

## 2. Create Google sign-in credentials

In Google Cloud Console, create an OAuth client with application type **Web application**.

- Authorized JavaScript origin: `https://sseonie.github.io`
- Authorized redirect URI: `https://qseicurjosmzwiqtflqr.supabase.co/auth/v1/callback`

Copy the Google Client ID and Client secret.

## 3. Enable Google in Supabase

In Supabase, open **Authentication** → **Providers** → **Google**.

- Enable Google.
- Paste the Client ID and Client secret.
- Save.

Then open **Authentication** → **URL Configuration**. Set **Site URL** to this address and add the same address under Redirect URLs:

`https://sseonie.github.io/slowly-habit-tracker/`

You can now open the app on both phone and computer, choose **Sign in**, and use the same Google account. The first signed-in device uploads its existing local records; later changes sync automatically.
