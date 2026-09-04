# StoreMate Android Deployment Flow

## Decision

The Android app is deployed separately from Azure. Azure hosts the API and database; Android phones receive the .NET MAUI application.

```text
.NET MAUI Android app -> signed APK for pilot
.NET MAUI Android app -> signed AAB -> Google Play for production
```

The app communicates with the API over HTTPS and never connects directly to SQL Server.

## Deployment Phases

### 1. Development

```text
.NET MAUI Debug build -> USB-connected physical Android phone
```

- Enable Developer Options and USB debugging.
- Use a development API URL reachable from the phone.
- Do not use `localhost` from the phone to access the PC API; use the PC LAN address or a development HTTPS endpoint.
- Test camera scanning and API authentication on real hardware.

### 2. Controlled Client Pilot

```text
Release build -> signed APK -> selected client devices
```

Use a signed APK when the client has a small, controlled number of phones. This avoids Play Store setup during early acceptance testing. Record the app version and install the same release on each pilot device.

### 3. Production Distribution

```text
Release build -> signed AAB -> Play Console internal testing
              -> closed testing -> production
```

Google Play requires an Android App Bundle for new store distribution. Internal testing is suitable for the team and early client users; move to closed testing before production.

## Costs

| Item | Cost |
|---|---:|
| .NET MAUI, Android SDK, VS Code | No license cost |
| Direct APK distribution | No separate hosting cost |
| Google Play Developer account | $25 one-time registration fee |
| Google Play app hosting and updates | No separate monthly hosting fee |
| Android devices and scanners | Client hardware cost |
| Azure API and database | Separate recurring web deployment cost |
| GitHub Actions | Free within included quotas; charges may apply beyond quota |

The client should own the Google Play Developer account. This prevents ownership problems if development support changes later.

## Signing and Release Security

- Create a production keystore and protect it outside the repository.
- Never commit the keystore, passwords, tokens, or signing files to Git.
- Store signing secrets in GitHub Actions secrets or another secure secret store.
- Back up the keystore securely; losing it can prevent future updates.
- Increase the Android version code for every published build.
- Keep separate development and production API configuration.

## Barcode and Future Hardware

The first release should support camera scanning on the phone. Also support HID-compatible Bluetooth or USB scanners where possible; these usually send the barcode as keyboard input and require no vendor SDK.

Keep hardware behind client-side interfaces:

```text
IBarcodeScanner
IReceiptPrinter
IApiClient
```

The application should pass only the normalized barcode value to the API. The API performs product lookup, stock validation, sale creation, and atomic inventory decrement. Later scanner, printer, or POS hardware can then be added without changing domain rules.

## Release Checklist

- [ ] Mobile project is a real .NET MAUI Android application.
- [ ] Release API URL uses HTTPS.
- [ ] Login, roles, barcode lookup, and sale flow tested on a physical phone.
- [ ] App identifier and version code are configured.
- [ ] Release build is signed.
- [ ] Keystore is backed up securely and excluded from Git.
- [ ] Privacy policy and data-safety information are prepared for Play Console.
- [ ] Internal testers and support contact are defined.
- [ ] Crash logging and API monitoring are enabled.

## Engineering Ownership

Android deployment is a client release process, not an Azure server deployment. GitHub Actions can eventually build, test, sign, and publish the AAB, but the first pilot can be built and installed manually. Automate after the app identity, signing process, and release checklist are stable.
