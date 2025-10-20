# WordPress .htaccess Configuration Guide

## Overview
This .htaccess file is optimized for **WHM/cPanel servers running LiteSpeed Web Server** with the **LiteSpeed Cache plugin v7.6.2** (or compatible versions).

---

## File Structure

### 1. **LiteSpeed Cache Plugin Sections (Lines 1-79)**
```
# BEGIN LSCACHE ... # END LSCACHE
# BEGIN NON_LSCACHE ... # END NON_LSCACHE
```

**⚠️ DO NOT MANUALLY EDIT THESE SECTIONS**
- These are automatically managed by the LiteSpeed Cache WordPress plugin
- Changes will be overwritten when you modify plugin settings
- The plugin updates these sections when you save cache settings in WordPress admin

**Key Features:**
- **ASYNC**: Handles admin-ajax.php requests properly
- **MOBILE**: Separate cache for mobile devices
- **WEBP**: Smart WebP image serving based on browser support
- **DROPQS**: Strips tracking parameters from cache keys while keeping them in URLs
  - ✅ **Google Ads conversion tracking works correctly**
  - ✅ Cache efficiency maintained
  - Parameters handled: `fbclid`, `gclid`, `utm_*`, `_ga`
- **BROWSER CACHE**: Sets 1-year expiration for static assets

---

### 2. **Base Security Rules (Lines 81-121)**
**✅ Safe to modify**

- **Directory Listing**: Disabled for security
- **Symlinks**: Enabled (required on shared hosts)
- **Proxy/CDN Detection**: Detects HTTPS via `X-Forwarded-Proto` header (Cloudflare compatible)
- **File Protection**: Blocks access to:
  - `.git`, `.svn`, dotfiles (except `.well-known`)
  - `composer.json/lock`, `package.json`, `yarn.lock`
  - `.env`, `wp-config.php`, `readme.html`, license files

---

### 3. **Canonical Redirects (Lines 123-155)**
**✅ Safe to modify based on needs**

**Currently Active:**
- Force HTTPS (safe with proxies like Cloudflare)

**Available Options (commented out):**
- Force non-www: Uncomment Option B1 if needed
- Force www: Uncomment Option B2 if needed
- **⚠️ Only enable ONE option at a time**

---

### 4. **WordPress Core Rewrites (Lines 157-173)**
```
# BEGIN WordPress ... # END WordPress
```

**⚠️ DO NOT MANUALLY EDIT**
- Managed by WordPress core
- Updates automatically when you change permalink settings in WordPress admin
- Essential for pretty permalinks to work

---

### 5. **REST API / Admin-Ajax Caching (Lines 175-197)**
**✅ Safe to modify**

- Prevents caching of `/wp-json/` (REST API) requests
- Prevents caching of `admin-ajax.php` requests
- **Important for**: Rank Math, contact forms, dynamic content, AJAX functionality

---

### 6. **CORS (Lines 199-208)**
**✅ Safe to enable if needed**

- Currently commented out
- Enable only if you have a separate frontend (React, Vue, etc.) that needs to access your REST API
- Leave disabled for standard WordPress sites

---

### 7. **XML-RPC (Lines 210-215)**
**✅ Safe to enable if needed**

- Currently commented out
- Enable to block XML-RPC attacks
- **⚠️ Leave disabled if you use**: Jetpack, WordPress mobile app, or remote posting

---

### 8. **Security Headers (Lines 217-228)**
**✅ Safe to modify**

Current headers:
- `X-Content-Type-Options: nosniff` (prevents MIME sniffing)
- `X-Frame-Options: SAMEORIGIN` (prevents clickjacking)
- `Referrer-Policy: strict-origin-when-cross-origin`
- `X-XSS-Protection: 1; mode=block`

**CSP (Content Security Policy)**: Commented out - enable carefully as it can break admin/editor

---

### 9. **PHP Version Handler (Lines 230-235)**
**✅ Safe to modify**

**Current Configuration:**
- PHP 8.3 via EasyApache (`application/x-httpd-ea-php83`)
- Handles: `.php`, `.php8`, `.phtml` files

**To Change PHP Version:**
Replace `ea-php83` with:
- `ea-php82` for PHP 8.2
- `ea-php81` for PHP 8.1
- `ea-php80` for PHP 8.0

---

## Important Notes

### 🔥 Conversion Tracking
- **Google Ads tracking is SAFE** with this configuration
- The LiteSpeed `DROPQS` section uses `CacheKeyModify` which:
  - Keeps tracking parameters in URLs (visible to Google Tag Manager)
  - But ignores them for cache purposes (efficiency)
  - Your `gclid`, `utm_*`, `fbclid` parameters work correctly

### 🔄 Plugin Interaction
When LiteSpeed Cache plugin is active:
1. It will automatically manage sections between its markers
2. If you change cache settings in WP admin, the plugin rewrites those sections
3. Your custom security/PHP/redirect rules will remain untouched

### 🛠️ Making Changes
**Before editing:**
1. Always backup the current file
2. Test changes on staging environment first
3. Check that LiteSpeed sections remain intact after edits

**After editing:**
1. Clear LiteSpeed cache in WordPress admin
2. Test site functionality (forms, AJAX, checkout, etc.)
3. Verify Google Ads conversion tracking still works
4. Check GTM/Analytics data collection

### ⚡ Performance Tips
- This file is optimized for LiteSpeed - don't add generic Apache cache rules
- Let the plugin handle caching - it's more efficient than manual rules
- WEBP images are automatically served to supporting browsers
- Mobile devices get separate cache (faster mobile experience)

---

## Troubleshooting

### Problem: Site not forcing HTTPS
- Check lines 134-138 (HTTPS redirect rules)
- If using Cloudflare, ensure SSL mode is "Full" or "Full (Strict)"

### Problem: Google Ads conversions not tracking
- Verify DROPQS section uses `CacheKeyModify` not `RewriteRule` redirects
- Check Google Tag Manager fires before cache is served
- Test with `?gclid=test123` in URL - parameter should remain visible

### Problem: Forms/AJAX not working
- Check REST API no-cache rules (lines 175-197)
- Verify admin-ajax.php is not cached
- Check if security plugin is blocking requests

### Problem: Plugin overwrites custom rules
- Keep custom rules OUTSIDE the LiteSpeed marker sections
- Custom rules should be between lines 81-228 (not in LiteSpeed blocks)
- Security rules are safe in their current location

### Problem: Wrong PHP version
- Update handler in lines 233 (change `ea-php83` to desired version)
- Verify PHP version in WHM/cPanel matches .htaccess setting
- Clear OPcache after PHP version changes

---

## Server Environment
- **Server**: WHM/cPanel with EasyApache
- **Web Server**: LiteSpeed Web Server
- **PHP Version**: 8.3
- **WordPress Plugin**: LiteSpeed Cache v7.6.2+
- **CDN/Proxy**: Compatible with Cloudflare and similar services

---

## Maintenance Schedule

### Monthly:
- [ ] Verify LiteSpeed Cache plugin is up to date
- [ ] Check that conversion tracking still works
- [ ] Review server error logs for .htaccess issues

### Quarterly:
- [ ] Test all forms and AJAX functionality
- [ ] Verify security headers are current best practices
- [ ] Check PHP version compatibility with WordPress/plugins

### When Updating:
- ✅ **Always backup before changes**
- ✅ Test on staging first
- ✅ Clear all caches after updates
- ✅ Verify tracking and analytics work

---

## Contact & Support
- LiteSpeed Cache Documentation: https://docs.litespeedtech.com/lscache/
- WordPress .htaccess Reference: https://wordpress.org/support/article/htaccess/
- WHM/cPanel Support: Contact hosting provider

---

**Last Updated**: October 20, 2025  
**Configuration Version**: 1.0  
**LiteSpeed Cache Plugin**: v7.6.2

