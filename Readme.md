<div align="center">
    <h1>🔅 GreatFilter for YouTube 🔅</h1>
    <p>Filter out low view count videos from the YouTube homepage</p>
</div>

<div align="justify">


## 🔥 Description

🔅 GreatFilter is a Tampermonkey script that enhances your YouTube browsing experience byfiltering out _(dimming or removing)_ videos with low view counts from the homepage or shorts

**Features**:

- Automatically filters videos based on a customizable view count threshold
- Provides multiple options for handling filtered videos (dimming, hiding, or removing)
- Respects user choices by not affecting subscriptions feed or channel pages

## 📦 Installation

### Prerequisites

- Install a user script manager extension:
  - [**Tampermonkey for Chrome**](https://chrome.google.com/webstore/detail/tampermonkey/dhdgffkkebhmkfjojejmpbldmpobfkfo)
  - [**Tampermonkey for Firefox**](https://addons.mozilla.org/en-US/firefox/addon/tampermonkey/)

### Installation Steps

1. Click on the extension icon in your browser toolbar and select "Create a new script"

2. Replace any existing code with the GreatFilter script below:
    ```js
    // ==UserScript==
    // @name         GreatFilter
    // @description  Remove Low view count videos from the YouTube homepage
    // @version      1.0
    // @author       Tremeschin
    // @match        *://*.youtube.com/*
    // @namespace    http://tampermonkey.net/
    // @icon         https://www.google.com/s2/favicons?sz=64&domain=youtube.com
    // ==/UserScript==

    (function() {
        'use strict';

        // Act on videos that have less than this amount of views
        const VIEW_THRESHOLD = 5000;

        function isBadVideo(views) {

            // Match for 'N.Nuu views' (1.3K, 2.0M, 1Mrd) until the next space ('views' word)
            let [, number, unit] = views.innerText.trim().match(/^(\d+(?:\.\d+)?)\s*([^\s]+)?/)
            number = parseFloat(number);

            // Find the true total number of views
            switch (unit.toLowerCase()) {
                case 'k':
                    number *= 1000;
                    break;
                case 'm':
                case 'mm':
                    number *= 1000000;
                    break;
                case 'b':
                case 'g':
                case 'mrd':
                case 'md':
                    number *= 1000000000;
                    break;
            }

            return (number < VIEW_THRESHOLD);
        }

        function filterVideos() {

            // Skip if intentionally seeing subscriptions or a channel page
            if (location.pathname.startsWith("/feed/subscriptions")) {
                return;
            }
            if (location.pathname.startsWith("/@")) {
                return;
            }

            // Act on the homepage or shorts
            if (location.pathname.startsWith("/shorts")) {
                document.querySelectorAll('.reel-video-in-sequence.style-scope.ytd-shorts').forEach(video => {
                    if (video.isActive) {
                        const views = video.querySelector('.yt-spec-button-shape-with-label__label');
                        if (views && isBadVideo(views)) {
                            const nextButton = document.querySelector('.navigation-button.style-scope.ytd-shorts .yt-spec-touch-feedback-shape__fill');
                            if (nextButton) nextButton.click();
                        }
                    }
                });
            } else {
                document.querySelectorAll('ytd-compact-video-renderer, ytd-rich-item-renderer').forEach(videoRenderer => {
                    const views = videoRenderer.querySelector('.inline-metadata-item.style-scope.ytd-video-meta-block');
                    if (views && isBadVideo(views)) {
                        // Note: Select some options below:

                        // // Send the video to the shadow realm
                        // videoRenderer.remove();

                        // // More gracefully to the shadow realm
                        // videoRenderer.style.display = 'none';

                        // // Fair compromise, still visible but dimmed
                        videoRenderer.style.opacity = '0.13';

                        // // Remove clickable links
                        // videoRenderer.querySelectorAll('a').forEach(a => a.removeAttribute('href'));
                    }
                });
            }
        }

        // Run the filter function whenever the page changes
        const observer = new MutationObserver(filterVideos);
        observer.observe(document.body, {childList: true, subtree: true});
    })();
    ```

3. Optionally modify the `VIEW_THRESHOLD` and comment/uncomment the desired filtering behavior at the end of the `filterVideos` function (dim, remove, or hide videos)

4. Save the script and reload any YouTube pages

## ⚖️ Ethical Considerations

The default dimming approach strikes a balance between user experience and fairness to content creators. It acknowledges that while videos with higher view counts often (but not always) indicate higher quality or relevance, it doesn't completely dismiss less popular content

## 📜 License

This script is provided as-is, without any warranties, under the [**MIT**](License.md) license. Users should be aware that YouTube's layout and functionality may change, potentially affecting the script's performance. Always ensure you're using the latest version of the script.

</div>
