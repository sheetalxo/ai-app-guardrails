---
name: android-play-store-app
description: Rules for building Android apps that are secure, polished and ready to publish on Google Play. Use this skill whenever the user wants to build, fix or publish an Android app, APK or AAB, mobile app, Play Store release, React Native/Expo, Flutter, Kotlin/Jetpack Compose or Capacitor app, mobile login, mobile payments, push notifications or app backend — even if they only say "app" and mention phones. Use together with secure-app-builder for the backend and general security.
---

# Android app for Google Play

Anyone can unzip an APK and read everything inside it, and a phone is a hostile environment (rooted devices, intercepted networks, lost phones). Design as if the app is public and the server is the only thing you trust.

## Workflow
1. **Frame it.** State: app purpose, users, offline needs, sensitive data, monetization, backend. Choose the stack and give a one-line reason (web developer wanting speed: Capacitor or Expo; best native feel: Kotlin + Jetpack Compose; one codebase with custom UI: Flutter). List assumptions instead of interrogating.
2. **Backend first.** The app talks only to your own HTTPS API. Apply `secure-app-builder` for the server (auth, authorization, validation, rate limits).
3. **Build screens from a design system** (see UI section), then features.
4. **Release checklist** at the end, and a short "what you must do yourself" list (signing key, Play Console, privacy policy).

## Security rules (and why)
- **No secrets inside the app.** API keys, DB credentials, admin tokens, LLM keys in code, `strings.xml`, `BuildConfig` or JS bundles are all extractable. Keep them on your server; the app gets a short-lived user token only.
- **Never trust the app.** Prices, roles, discounts, premium status are decided and checked by the server. Verify in-app purchases server-side with Google Play's API.
- **Secure token storage.** Use Android Keystore-backed storage (EncryptedSharedPreferences / `expo-secure-store` / `flutter_secure_storage`). Never put tokens in plain SharedPreferences, AsyncStorage, localStorage or logs.
- **HTTPS only.** Do not enable cleartext traffic. Set a `network_security_config` that blocks cleartext; consider certificate pinning only if you can rotate pins safely.
- **Login:** short-lived access token + refresh token, server-side revocation, logout clears storage. Prefer OAuth/Google Sign-In or a proven auth provider over homemade auth.
- **Minimal permissions.** Request only what the feature needs, at the moment it is needed, with a clear explanation. Remove unused permissions from the manifest. Fewer permissions also make Play review easier.
- **Components:** `android:exported="false"` unless another app must call it; validate every Intent/deep-link input; never load arbitrary URLs in a WebView with JavaScript enabled and never expose `addJavascriptInterface` to untrusted pages.
- **Release builds:** minify/obfuscate (R8/ProGuard, Hermes for RN), `debuggable=false`, `allowBackup=false` unless needed, remove logs of personal data, no test endpoints or debug menus.
- **Local data:** store as little as possible; encrypt sensitive local DBs (SQLCipher) when required. Clear data on logout.
- **Screenshots/clipboard:** use `FLAG_SECURE` on screens showing payment or private data when appropriate.
- **Firebase/Supabase style backends:** write security rules deny-by-default and test them; public config keys are fine only because rules enforce access.

## Reliability
- Handle no network, slow network, timeouts, expired token, server errors, and app killed mid-action. Retry with backoff; never lose user input.
- Respect lifecycle and rotation; avoid work on the main thread; paginate lists; compress and cache images.
- Crash and error reporting (Crashlytics/Sentry) without personal data.
- Version and migrate local DB schemas properly.

## UI / UX for Android
- Follow Material 3 conventions: top app bar, bottom navigation (3–5 destinations), FAB for the primary action, sheets and snackbars for transient feedback.
- Touch targets ≥ 48dp, comfortable spacing on an 8dp grid, text in `sp` (respect system font size), body ≥ 16sp.
- Support the system back button/gesture everywhere and edge-to-edge layouts with correct insets.
- Light and dark theme from one token set; contrast ≥ 4.5:1. Use dynamic color only if it keeps brand clarity.
- Every list/screen has loading (skeleton), empty, error + retry, and success states.
- Forms: right keyboard type, autofill hints, inline errors, submit button disabled while pending.
- Test on a small phone (≈360dp wide), a large phone and tablet/foldable; check TalkBack labels (`contentDescription`).
- Apply `secure-app-builder/references/ui-ux.md` principles (tokens, hierarchy, whitespace, no clutter).

## Google Play release checklist
- [ ] App signed with Play App Signing; upload keystore backed up in two safe places (losing it blocks updates). Never commit the keystore or passwords.
- [ ] Build an **AAB** (not APK) for the store; increment `versionCode` every release.
- [ ] `targetSdkVersion` meets Play's current requirement (it rises yearly — verify in Play Console before building).
- [ ] Privacy policy URL (public page), accurate **Data safety** form, content rating questionnaire.
- [ ] If users can create an account: in-app account deletion plus a web deletion link.
- [ ] Store listing: icon 512×512, feature graphic 1024×500, 2+ real screenshots, honest short and full description.
- [ ] Newly created personal developer accounts must run a **closed test** with a minimum number of testers for a minimum period before production access — check the current numbers in Play Console help and start early.
- [ ] Digital goods must use Google Play Billing; do not link to outside payment for them.
- [ ] Test the release build (not debug) on a real device, including a fresh install and an update from the previous version.

## Output format
1. Assumptions and chosen stack
2. Project structure and API contract
3. Complete code files (path as heading)
4. Security & quality notes, plus the user's remaining to-dos (signing, Play Console, privacy policy)
