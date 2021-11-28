# GNU IceCat: A Browser that Respects Your Freedom

GNU IceCat is a web browser based on Firefox Enterprise Edition (Firefox ESR) sans Firefox trademark logo and out-of-the-box ability to install non-free addons. Firefox ESR is a more stable release of Firefox Quantum i.e. older version. I find default extensions annoying at times.

## Default Extensions

- LibreJS. It blocks suspecting non-trivial javascript codes. My first experience is that LibreJS blocks almost everything. It makes browsing almost impossible. Sure, you can unblock a particular javascript code but it is unfun and time-consuming to do that often. The javascript is usually minified so I don't see how can I verify it. As a result, I remove LibreJS, and also related add-ons, from IceCat.
- Searxes' Third-party Request Blocker. This extension blocks all 3rd party requests. Google tag manager and Google analytics are found on almost every website. Facebook and co. trackings are found on many websites which I keep blocked. Requests that I allow include necessary CAPTCHA testing requests and the extension that does A/B testing. I allow most third-party CDN requests too but I do out of necessity in this case. This extension is much less annoying than LibreJS so I keep it.
- ViewTube. It makes you anonymous while browsing Youtube. I disable it so that I can log in and like the video and sometimes even watch the ads to support the creator. I don't mind Google tracking in this case.
- And more. I don't cover them here.

## Login with Firefox Account

Last but not least, by default, IceCat doesn't allow you to login using a Firefox credential as the default settings refuse to fingerprint but you can type about:config in the URL and then set the below values to bypass it. I don't do this but if you want, you can.

```text
privacy.resistFingerprinting = false
identity.fxaccounts.enabled = true
```

## Low-Pitched Audio Issue

Sometimes I experienced video playback from some websites having low-pitched sound. This can be changed by setting `media.mediasource.enabled = false`. This might break some websites, in that case just enable it again.
