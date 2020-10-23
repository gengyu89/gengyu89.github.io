**Minimal edit for the 1st launch**
  - `_config.yml`
    1. `featured-condition-size: 1` -
      this is the minimum frequency for a tag to be listed as "featured"
  - Clean up `_doc`
  - `_includes/about/*.md`
  - `_includes/footer.html` - update the footer link and text
  - Clean up `_includes/posts/`
  - `404.html`, `about.html`, `archive.html`, `index.html`, and `offline.html`
  - Delete `CNAME`
  - Delete all existing articles under the `_posts` folder but keep `hidden`
  - `sw.js` - you can edit this file
    but do not remove "huangxuan.me" from `HOSTNAME_WHITELIST`

**Files you should not modify**
  - `package-lock.json` and `package.json`

**Update profile photo**
  1. Prepare the image
  2. Update `sidebar-avatar` in _config.yml
  3. Delete unused files from `sw.js`

**Preview locally**
  1. Run `jekyll serve` in the root directory
  2. Type `http://localhost:4000` in your browser

**Release**
  - Please always use "highly insultive" as commitment messages
