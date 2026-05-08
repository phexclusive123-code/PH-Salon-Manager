# PH Salon Manager - Point of Sale System

A full-featured Salon POS (Point of Sale) mobile app built with Expo React Native, featuring staff management, client CRM, appointment scheduling, checkout, receipt printing, and analytics reporting.

## Architecture

- **Frontend**: Expo Router (file-based routing), React Native
- **Backend**: Express.js on port 5000 (serves landing page only; API not used)
- **Storage**: AsyncStorage for local persistence (no external DB required)
- **State**: React Context (SalonContext + SettingsContext) for all app data

## Key Files

- `context/SalonContext.tsx` - Central data store with all business logic; `addTransaction` returns the new transaction ID
- `context/SettingsContext.tsx` - Salon settings persisted under `salon_settings_v2`; logo stored separately under `salon_logo_v2` to avoid AsyncStorage size limits; uses `useRef` + `useCallback` to avoid stale closure bugs
- `constants/colors.ts` - Rose/gold color theme (light & dark)
- `hooks/useCurrency.ts` - Currency hook: `fmt(amount)`, `symbol`, `fmtInt(amount)`
- `app/(tabs)/index.tsx` - Dashboard with 4 clickable stat tiles (modals for sales, appointments, staff, weekly revenue)
- `app/(tabs)/pos.tsx` - POS screen with service/product catalog, cart, staff assignment
- `app/(tabs)/appointments.tsx` - Appointment scheduling with status management
- `app/(tabs)/clients.tsx` - Client CRM
- `app/(tabs)/staff.tsx` - Staff management (accessible via Dashboard)
- `app/(tabs)/catalog.tsx` - Service & product catalog tab
- `app/checkout.tsx` - Full checkout flow with receipt print button after payment
- `app/receipt-preview.tsx` - Receipt preview with print/share (configurable paper size, logo, header/footer)
- `app/transaction-detail.tsx` - Transaction detail with print button
- `app/new-appointment.tsx` - Create/edit appointment modal
- `app/client-detail.tsx` - Client profile modal
- `app/staff-detail.tsx` - Staff profile modal
- `app/settings.tsx` - Full settings modal (logo, business info, currency, tax, receipt/printer config, admin mode)
- `app/catalog.tsx` - Catalog root modal (separate from tab)
- `public/sw.js` - Service worker for PWA offline support

## Settings Storage Fix (v2)
- Old key `salon_settings` replaced with `salon_settings_v2`
- Logo stored separately as `salon_logo_v2` (avoids large JSON causing silent AsyncStorage failures)
- `updateSettings` uses `useRef` to always have latest settings (prevents stale closure bug)
- `useCallback` ensures `updateSettings` is stable across renders
- `_layout.tsx` waits for `isLoading=false` before rendering app (prevents flash of default settings)

## Features

1. **Dashboard** - 4 clickable stat tiles:
   - Today's Revenue → daily sales chart with hourly breakdown + transaction list
   - Appointments → today's appointment timeline with status overview
   - Staff On Duty → all employees, clock-in status, navigate to profiles
   - Week Revenue → 7-day bar chart, daily breakdown (admin-only gate)
   - **Open POS Tabs section** — shows active tabs with item count & staff
2. **POS** - Multi-tab (up to 4 simultaneous tabs), service/product catalog, cart, staff assignment per tab; tabs persisted across app restarts; close tab with confirmation
3. **Appointments** - Date picker, status management, booking
4. **Clients** - CRM with loyalty points, visit history, spending; delete client with single tap + confirmation
5. **Staff** - Roles, commission tracking, clock-in/out
6. **Catalog** - Services (by category) and products with full CRUD
7. **Receipt/Bill Printer** - Configurable paper size (58mm/80mm/A4), logo, header/footer text, tax/tips/staff/barcode toggles; Print/Share; print from transaction history
8. **Settings** - Business info, contact, currency (12 currencies; default LKR), tax rate (default 0%), receipt printer config, Admin Mode toggle
9. **Checkout** - Payment methods: Cash, Card, Mobile Pay, Bank Transfer, Split Bill; Split Bill assigns items to Person A/B with separate charges and payment methods
10. **Themes** - 6 color themes (Rose/Ocean/Forest/Lavender/Sunset/Teal)

## Defaults (SettingsContext)
- `taxRate`: 0 (not 10) — persists via `salon_settings_v2`
- `currency`: 'LKR' — persists via `salon_settings_v2`
- `theme`: 'rose'

## POS Tabs (SalonContext)
- `posTabs: PosTab[]` stored in AsyncStorage under `salon_pos_tabs`
- `addPosTab(staffId?)` → returns new tab id
- `updatePosTab(id, updates)` → updates cart/staffId/label
- `closePosTab(id)` → removes from list
- Checkout passes `tabId` param; on success, calls `closePosTab(tabId)`

## PaymentMethod Type
`'cash' | 'card' | 'mobile' | 'bank_transfer' | 'split'`

## Admin Mode
- Toggle in Settings → Access Control
- When OFF: Weekly Revenue tile shows lock icon and prompts to enable admin mode
- When ON: Full weekly revenue chart with daily breakdown is accessible

## Daily Report — `app/daily-report.tsx`
- **Access**: Dashboard → Today's Revenue tile → "View Full Daily Report" button
- **Date navigation**: Swipe back/forward through any past day
- Total revenue hero card with receipt count, tips, tax pills
- 4 quick-stat cards: Receipts, Avg Bill, Tax, Tips
- Payment breakdown with progress bars (Cash / Card / Mobile / Bank)
- Top items sold with revenue ranking
- **Full transaction list**: ALL receipts for the day — each row shows time, client, payment badge, staff, items summary
- Each receipt has a **Print button** (opens receipt-preview) and a detail arrow
- Share as text or WhatsApp directly from the header

## Monthly Reports (admin-only) — `app/reports.tsx`
- **Access**: Dashboard → Monthly Reports (only visible when Admin Mode is ON)
- **Month selector**: navigate back/forward months
- **5 tabs**: Summary / Staff / Services / Products / Expenses
- Summary: Monthly financials, service vs product split, **Year-to-Date stats**, daily sales chart (revenue + expenses overlay)
- Staff: Per-staff revenue, service commission (10%), product commission (2%), **Mark Commission Paid / Unpaid** with persistent payment history
- Services: Service-wise income ranked by revenue with mini bar charts
- Products: Product-wise income with commission and bar charts
- Expenses: Add/delete daily expenses by category, **expense category breakdown chart**, expense log
- **WhatsApp share**: Full report including YTD, commission paid status, category breakdown
- Commission payments stored in AsyncStorage under `salon_commission_payments`

## Client Follow-Up (admin-only) — Dashboard
- Appears on Dashboard when Admin Mode is ON and clients haven't visited in 30+ days
- Shows days since last visit, sorted by most overdue
- One-tap **WhatsApp** follow-up (pre-filled personalised message) and **Call** buttons per client
- Max 8 clients shown

## Tablet Layout Support
All screens use `useWindowDimensions()` with `isTablet = width >= 768`:
- **Content screens** (login, checkout, settings, transaction-detail, receipt-preview, daily-report, reports): `paddingHorizontal` centering via `Math.max(base, (width - maxWidth) / 2)` — content stays ≤ their max width on wide displays
- **List screens** (clients, appointments, catalog): `paddingHorizontal` centering; clients uses `flexDirection: 'row', flexWrap: 'wrap'` with `width: '50%'` per card for 2-column grid
- **POS screen**: Two-panel layout — left panel (catalog browser, `flex: 1`) + right panel (`CartSidebar`, 300px fixed width) showing cart items, running total, and checkout button

## Data Persistence
- All data stored locally via AsyncStorage
- Works fully offline
- Can be installed as PWA on Windows/Mac via Chrome/Edge install button
