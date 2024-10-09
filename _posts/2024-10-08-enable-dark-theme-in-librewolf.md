---
title: Enable Dark Theme in LibreWolf
published: true
---
# Enable Dark Theme in LibreWolf


LibreWolf, a privacy-focused fork of Firefox, aims to resist browser fingerprinting by default. One of its protective measures is enforcing a light theme for all websites to prevent revealing the system’s theme preference. However, sometimes browsing with a forced light theme can be a bit annoying, especially if you prefer dark mode for more comfortable nighttime browsing.

So, how can you enable a dark theme while maintaining your privacy on the internet?


## FPP Over RFP


Using **RFP** (Resist Fingerprinting) in LibreWolf makes it impossible to apply a dark theme because it locks the browser's settings, including color schemes, to avoid giving away any identifiable preferences.

However, there’s a workaround! By switching to **FPP** (Fingerprinting Protection) and tweaking a few settings, you can enable the dark theme without sacrificing all your fingerprinting protection.

### How to Enable Dark Theme with FPP


Follow these steps to set it up:

Open LibreWolf and go to `about:config`.

Modify the following configuration settings:

```
privacy.resistFingerprinting = false
privacy.fingerprintingProtection = true
privacy.fingerprintingProtection.overrides = +AllTargets,-CSSPrefersColorScheme
```

By disabling `resistFingerprinting` and enabling `fingerprintingProtection` with the proper overrides, you can restore the color scheme preferences while still retaining the majority of the anti-fingerprinting features.

This tweak allows you to enjoy a dark theme without losing all the protections against browser fingerprinting.


## Why Does This Work?


The `fingerprintingProtection` option in LibreWolf is more flexible than `resistFingerprinting`. It allows users to adjust specific fingerprinting aspects while keeping others in place. By overriding the CSS prefers-color-scheme, you permit websites to detect your theme preference (light or dark), while still protecting against other types of fingerprinting.

Source: [Mozilla Bugzilla Issue #1732114](https://bugzilla.mozilla.org/show_bug.cgi?id=1732114)

With this setup, you can have the best of both worlds—comfortable dark mode browsing and decent privacy protection!
