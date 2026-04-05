# Tatze Bot Companion

Tatze Bot Companion ist eine zweigeteilte Plattform:

- `apps/desktop`: Electron-Desktop-App fuer Login, Verwaltung und Konfiguration
- `services/backend`: Node.js API + Discord Bot Runtime fuer Twitch-, Service- und Ticket-Module
- `packages/shared`: gemeinsame Defaults, Bot-Namen und Hilfsfunktionen
- `supabase/migrations`: SQL fuer Supabase

## Architektur

- Die Desktop-App fuehrt Discord OAuth2 ueber den Backend-Service aus.
- Der Backend-Service speichert Benutzer und Einstellungen in Supabase.
- Ein einzelner Discord-Bot verarbeitet alle Module serverseitig.
- Twitch und YouTube werden ueber offizielle APIs bzw. Feeds abgefragt.
- TikTok-Benachrichtigungen laufen ueber RSSHub-kompatible Feeds, weil TikTok keine allgemeine offizielle Public-Creator-Feed-API fuer beliebige Accounts anbietet.

## Schnellstart

1. Fuehre die SQL-Migration aus `supabase/migrations/20260331_initial.sql` in deinem Supabase-Projekt aus.
2. Erstelle `services/backend/.env` auf Basis von `services/backend/.env.example`.
3. Erstelle optional `apps/desktop/.env` auf Basis von `apps/desktop/.env.example`.
4. Fuer lokale Overrides kannst du optional `services/backend/.env.local` und `apps/desktop/.env.local` nutzen (siehe jeweilige `.env.local.example`).
5. Installiere die Abhaengigkeiten mit `npm install`.
6. Starte zuerst den Backend-Service mit `npm run dev:backend`.
7. Wenn du bereits in `services/backend` bist, funktionieren dort jetzt sowohl `npm run dev` als auch `npm run dev:backend`.
8. Starte danach die Desktop-App mit `npm run dev:desktop`.
9. Wenn du bereits in `apps/desktop` bist, funktionieren dort jetzt sowohl `npm run dev` als auch `npm run dev:desktop`.

## Debug Hinweise

- Discord OAuth akzeptiert nur exakt die Redirect URI aus `services/backend/.env`.
- Produktive Basis-URL ist `https://tatze.eu` (Health: `https://tatze.eu/health`).
- Fuer lokale Entwicklung kannst du die Redirects in `services/backend/.env.local` auf `http://127.0.0.1:4100/...` setzen.
- Das Backend stellt Bot-Guilds und Bot-Channels ueber `GET /v1/guilds` und `GET /v1/guilds/:id/channels` bereit.
- Wenn ein Invite im Browser geoeffnet wurde, synchronisiert die Desktop-App den Bot-Installationsstatus automatisch nach.

## Live-Konfiguration (tatze.eu)

- Basis-URL: `https://tatze.eu`
- Discord Callback: `https://tatze.eu/v1/auth/discord/callback`
- Twitch Callback: `https://tatze.eu/v1/auth/twitch/callback`
- Stripe Webhook: `https://tatze.eu/v1/payments/stripe/webhook`

## Erforderliche Secrets

- `DISCORD_CLIENT_ID`
- `DISCORD_CLIENT_SECRET`
- `DISCORD_BOT_TOKEN`
- `SESSION_SECRET`
- `SUPABASE_SERVICE_ROLE_KEY` fuer produktive sichere Schreibzugriffe
- `TWITCH_CLIENT_ID`
- `TWITCH_CLIENT_SECRET`

## Optionale Secrets / Konfiguration

- `YOUTUBE_API_KEY` wenn YouTube-Handles serverseitig ueber die Data API aufgeloest werden sollen
- `TIKTOK_RSS_BASE_URL` fuer eigene oder alternative RSSHub-Instanzen
- `DISCORD_BOT_INVITE_PERMISSIONS` fuer angepasste Invite-Rechte

## Discord Anwendung

Registriere fuer den OAuth2-Flow einen Redirect auf:

- Produktion: `https://tatze.eu/v1/auth/discord/callback`
- Lokal (optional): `http://127.0.0.1:4100/v1/auth/discord/callback`

Fuer Twitch OAuth:

- Produktion: `https://tatze.eu/v1/auth/twitch/callback`
- Lokal (optional): `http://127.0.0.1:4100/v1/auth/twitch/callback`

Fuer Stripe Webhooks:

- Produktion: `https://tatze.eu/v1/payments/stripe/webhook`

Die Desktop-App erwartet danach den Ruecksprung ueber das Custom Protocol:

- `tatzebotcompanion://auth?login=<id>`

## Hinweise fuer Produktion

- Aktiviere beim Bot mindestens die Intents fuer `Guilds`, `GuildMembers`, `GuildMessages` und `MessageContent`.
- Hinterlege fuer Supabase den `service_role` Key nur im Backend, nie in der Desktop-App.
- Achte darauf, dass `SUPABASE_URL`, `SUPABASE_ANON_KEY` und `SUPABASE_SERVICE_ROLE_KEY` zum selben Supabase-Projekt gehoeren.
- Verwende fuer TikTok nach Moeglichkeit eine eigene RSSHub-Instanz, weil oeffentliche Instanzen bei Anti-Bot-Massnahmen schwanken koennen.
- Setze `PUBLIC_BASE_URL=https://tatze.eu`, damit OAuth Redirects und externe Links konsistent bleiben.
