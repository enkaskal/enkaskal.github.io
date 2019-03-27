---
layout: post
author: Stefan Schwoegler
title: "Disabling macOS Mojave dark mode for a specific app"
description: "HOWTO disable macOS Mojave dark-mode for a specific app (e.g. Google Chrome)"
keywords: "macOS mojave dark mode darkmode dark-mode specific app google chrome"
---

# HOWTO - disable macOS (Mojave) dark mode for a specific/single app

## Introduction

While macOS Dark mode is great, in general, I find myself confusing it with specific app modes. Namely, Google Chrome Icognito.

Therefore, the following instructions provide a quickstart/reminder of how to disable Dark Mode for particular apps.

## Find the application Bundle

Use the `osascript` command to find the Bundle name:

```
osascript -e 'id of app "Some Random App"'
```

For example, `Google Chrome`:

```
osascript -e 'id of app "Google Chrome"'
```

## Disable Dark mode for a Bundle

Now use the following command to disable the app `Bundle_Identifier` retrived from the previous step:

```
defaults delete Bundle_Identifier NSRequiresAquaSystemAppearance
```

Continuing the previous example:

```
defaults write com.google.Chrome NSRequiresAquaSystemAppearance -bool yes
```

## Restart

Unfortunately, one must restart the app in order for the new settings to take effect :(

## Resetting

For whatever reason, should you want to reset the Bundle to defaults use the following command:

```
defaults delete Bundle_Identifier NSRequiresAquaSystemAppearance
```

# References

All credit goes to the following post (this is just a tl;dr :)
 * https://www.tekrevue.com/tip/exclude-app-dark-mode-macos-mojave/
