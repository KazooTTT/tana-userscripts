# Tana Userscripts

A collection of userscripts to enhance your Tana Outliner experience.

## Available Scripts

### Tana Outliner - Title = Current Node

**Version:** 0.2  
**Author:** KazooTTT

Display the current Tana node title in your browser tab (format: `Tana - {title}`).

#### Features

- Automatically detects and shows the current node title
- Updates dynamically as you navigate between nodes
- Works with Tana's locked names and dynamic content

#### Installation

1. Install [Tampermonkey](https://www.tampermonkey.net/) or [Violentmonkey](https://violentmonkey.github.io/)
2. Click [Install Script](tampermonkey/index.js) (or copy the code below)
3. Grant permissions for `https://app.tana.inc/*`
4. Refresh Tana and enjoy!

#### Script Source

```javascript
// ==UserScript==
// @name         Tana Outliner - Title = Current Node
// @namespace    https://kazoottt.top
// @version      0.2
// @description  将当前节点 title 显示到浏览器标签的 HTML title 中（格式：Tana - {title}）
// @match        https://app.tana.inc/*
// @run-at       document-end
// @grant        none
// ==/UserScript==

(function () {
  'use strict';

  // 根据当前 span 的文本更新 document.title
  function updateTitleFromSpan(span) {
    if (!span) return;
    const text = span.textContent.trim();
    if (!text) return;
    document.title = `Tana - ${text}`;
  }

  // 尝试在页面上找到当前标题 span
  function findCurrentTitleSpan() {
    // <span data-role="editable" class="editable lockedName" ...>Today, Thu, May 21</span>
    return document.querySelector('span.editable.lockedName[data-role="editable"]');
  }

  function initOnce() {
    const span = findCurrentTitleSpan();
    if (span) {
      updateTitleFromSpan(span);
      observeTitleSpan(span);
    } else {
      const retryInterval = setInterval(() => {
        const s = findCurrentTitleSpan();
        if (s) {
          clearInterval(retryInterval);
          updateTitleFromSpan(s);
          observeTitleSpan(s);
        }
      }, 500);
      setTimeout(() => clearInterval(retryInterval), 30000);
    }
  }

  function observeTitleSpan(span) {
    const observer = new MutationObserver(() => {
      updateTitleFromSpan(span);
    });

    observer.observe(span, {
      characterData: true,
      subtree: true,
      childList: true,
    });

    const docObserver = new MutationObserver(() => {
      const newSpan = findCurrentTitleSpan();
      if (newSpan && newSpan !== span) {
        observer.disconnect();
        updateTitleFromSpan(newSpan);
        observeTitleSpan(newSpan);
      }
    });

    docObserver.observe(document.body, {
      childList: true,
      subtree: true,
    });
  }

  window.addEventListener('load', () => {
    setTimeout(initOnce, 1000);
  });
})();
```

## Contributing

Feel free to submit issues and pull requests!

## License

MIT