<div align="center">
  <h1>
    Bookmark Cross Browser Extension
  </h1>

  <p>
    <strong>A cross browser extension to make bookmarking easy and easily sync with <a href="https://github.com/mrgodhani/bookmark">Bookmark</a> desktop application.</strong>
  </p>
</div>

![img](screenshot.png)

## Installation
1. Clone the repository `git clone https://github.com/EmailThis/extension-boilerplate.git`
2. Run `npm install`
3. Run `npm run build`

##### Load the extension in Chrome & Opera
1. Open Chrome/Opera browser and navigate to chrome://extensions
2. Select "Developer Mode" and then click "Load unpacked extension..."
3. From the file browser, choose to `extension-boilerplate/build/chrome` or (`extension-boilerplate/build/opera`)


##### Load the extension in Firefox
1. Open Firefox browser and navigate to about:debugging
2. Click "Load Temporary Add-on" and from the file browser, choose `extension-boilerplate/build/firefox`


## Developing
The following tasks can be used when you want to start developing the extension and want to enable live reload - 

- `npm run chrome-watch`
- `npm run opera-watch`
- `npm run firefox-watch`


## Packaging
Run `npm run dist` to create a zipped, production-ready extension for each browser. You can then upload that to the appstore.

## How it works
1. Install extension as per above
2. After install when you first click on extension it would ask for firebase project id. Use same project id that is being used in [bookmark desktop application](https://github.com/mrgodhani/bookmark).
3. Start bookmarking any link. Bookmarked links would appear in desktop app real time.
