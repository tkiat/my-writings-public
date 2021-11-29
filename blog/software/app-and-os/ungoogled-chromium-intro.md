# Ungoogled Chromium: Introduction and Some Tips

Chromium, an open-source web browser alternative from Google Chrome, still contains Google web services. Ungoogled Chromium removes such dependency. You can download from their [repository](https://github.com/Eloston/ungoogled-chromium).

## Extensions

You cannot download extensions from the Chrome Web Store directly at first but there are some options I am aware of.

### Option 1: Use chromium-web-store extension

This extension is [mentioned](https://ungoogled-software.github.io/ungoogled-chromium-wiki/faq) in the official Ungoogled Chromium Doc. It allows you to install the extension directly instead of manually download the .crx files. Head over to their [repository](https://github.com/NeverDecaf/chromium-web-store) and install it. You can then download extensions directly from the Chrome Web Store.

### Option 2: Manually download and install .crx file

1. Go to Chrome Web Store and browser the extension
2. Observe URL and note the extension id `https://chrome.google.com/webstore/detail/[...]/[EXTENSION_ID]`
3. Find your Ungoogled Chromium version from chrome://settings/help
4. Download crx by typing in URL `https://clients2.google.com/service/update2/crx?response=redirect&acceptformat=crx2,crx3&prodversion=[YOUR_UNGOOGLED_CHROMIUM_VERSION]&x=id%3D[EXTENSION_ID]%26installsource%3Dondemand%26uc`
5. Prepare for the installation by setting flag `chrome://flags/#extension-mime-request-handling` to `Always prompt for install`
6. Download file by typing `file://PATH_TO_CRX` in the address bar, alternatively just drag and drop .crx file in `chrome://extensions/`

Or read how-to from their Wiki: [https://ungoogled-software.github.io/ungoogled-chromium-wiki/](https://ungoogled-software.github.io/ungoogled-chromium-wiki/)

### Option 3: Some extensions allow you to install from source e.g. [vimium](https://github.com/philc/vimium)

1. Navigate to chrome://extensions
2. Toggle the Developer Mode
3. Click on "Load Unpacked Extension..."
4. Select the Vimium directory you've cloned from GitHub.

## Configurations

### Cookies

All cookies are disabled by default but you can enable it for some websites (I enable DuckDuckGo and Startpage cookies for dark themes).

## Problems

- **The "make Chrome a default browser" prompt persists**: Use the command alias `chromium --no-default-browser-check`.
