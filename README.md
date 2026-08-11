# Deploying the online booking website

This is a second, separate website from your EMR — the public one patients use to book
appointments. It uses the **same Supabase project** as your EMR, so there's nothing new to set
up there beyond running one SQL file.

## Part 1 — Add the booking table to your existing Supabase project

1. Go to your Supabase project (the same one your EMR already uses) → **SQL Editor** → **New query**.
2. Open `schema-booking.sql` from this project, copy all of it, paste it in, and click **Run**.
   This creates the `patient_registrations` table (which only the public site can add to — it
   can never read patient data back) and a narrow `booked_slots_public` view that only exposes
   which times are taken, nothing personal.

## Part 2 — Put this project on GitHub

Same as your EMR setup: create a **new, separate** GitHub repository (e.g. `clinic-booking`),
and upload every file in this project except `node_modules` and `.env` (there won't be either
yet — just upload everything you were given).

## Part 3 — Deploy it (Vercel)

1. Vercel → **Add New** → **Project** → import your new `clinic-booking` repo.
2. Before deploying, add the same two environment variables as your EMR — copy the exact same
   values from Supabase (Project Settings → API):
   - `VITE_SUPABASE_URL`
   - `VITE_SUPABASE_ANON_KEY`
3. Click **Deploy**. You'll get a separate live link, e.g. `clinic-booking.vercel.app` — this is
   the page you'd link from Facebook, Google, or wherever patients find you.

## Part 4 — Before this goes live

- **Add your real GCash number.** Open `src/App.jsx` and find `gcashNumber` near the top —
  it's currently a placeholder. If you want a QR code image shown too, send it to me and I'll
  add it.
- **Test the whole flow yourself first**, with fake info: book a slot on the public site, then
  open your EMR → **Registrations** and approve it. Confirm the patient and appointment show up
  correctly in Patients and Schedule before pointing real patients at it.
- **Decide who checks the Registrations tab.** Nothing is automatic — a booking sits as
  "pending" until a staff member opens the EMR, checks the GCash reference against your actual
  payment records, and approves it. No one is notified automatically; someone needs to check
  that tab regularly (the sidebar shows a count of how many are waiting).

## What this does and doesn't do

- **Does:** collect the patient's info and preferred slot, hold it for staff review, and — once
  approved — automatically create the patient record and appointment in your EMR, exactly like
  the ones added by hand.
- **Doesn't:** actually process the GCash payment. There's no payment gateway here — the patient
  types in their own reference number, and it's on your staff to verify it actually matches a
  real payment before approving. Someone submitting a fake or already-used reference number is
  a real possibility worth watching for, especially early on.
- **Doesn't:** check whether the same person already has a patient record with you. Every
  approval creates a brand-new patient — if a returning patient books online, you'll end up with
  a duplicate record rather than it merging into their existing chart. Worth keeping an eye on
  until this is handled automatically.
