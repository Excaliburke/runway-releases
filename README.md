# Runway releases

Signed and notarised macOS builds of Runway, a Mac app for Coursera courses in
Coursera Teach, and the manifests the app reads when it checks for updates.
**The source lives in a private repository**; this one exists only to serve
downloads.

## Installing

Download the newest release from
[Releases](https://github.com/Excaliburke/runway-releases/releases/latest):

| your Mac | download |
|---|---|
| Apple silicon (M1 and later) | `Runway-<version>.dmg` |
| Intel | `Runway-<version>-x64.dmg` |

Open the disk image and drag Runway to Applications. After that, Runway looks
for a newer version a minute after it opens and every six hours, and says so
once: a notification, and Download in its ⋯ menu. Downloading and restarting
are always your choice.

## Why a separate repository

Squirrel.Mac, the updater Electron ships on macOS, fetches the manifest and the
update with no credentials. A private repository's release assets answer `404`
to a request with no credentials, so an update check against one could never
work. The other choices were a read-only token inside the app, where anyone
with a copy could take it out, or hosting somewhere else. Publishing only the
built app to a public repository keeps the source closed without shipping a
credential.

## What each asset is

| asset | what it is |
|---|---|
| `Runway-<version>.dmg`, `Runway-<version>-x64.dmg` | what a person downloads: the app and an Applications shortcut |
| `Runway-<version>-arm64.zip`, `Runway-<version>-x64.zip` | what the in-app updater installs |
| `latest-mac-arm64.json`, `latest-mac-x64.json` | version, download URL, SHA-256, size and release notes, one per architecture |

The app in each file is signed with a Developer ID certificate, notarised by
Apple, and has the notarisation ticket stapled into it, so it opens on a Mac
that has never seen it and on one that cannot reach Apple to ask. Each zip
holds `Runway.app` and nothing else.

## The URLs the app reads

Each build has its update URL built in, so the URL has to keep working after
the release it shipped with is no longer the newest:

```
https://github.com/Excaliburke/runway-releases/releases/latest/download/latest-mac-arm64.json
https://github.com/Excaliburke/runway-releases/releases/latest/download/latest-mac-x64.json
```

GitHub serves `/releases/latest/download/NAME` as the newest release's copy of
that file: a fixed address with changing contents. **Every release must carry
both manifests under exactly those names**, or copies already installed stop
finding updates. Runway reads the manifest and compares versions itself before
Squirrel is told anything.

## Checking a download yourself

```
spctl -a -vv -t exec /Applications/Runway.app
```

`source=Notarized Developer ID` is the answer you want. Anything else means the
copy you have is not the one published here.
