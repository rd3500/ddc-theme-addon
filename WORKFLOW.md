# DDC/Theme Workflow: How We Work Together

This document explains our collaborative workflow for customizing XenForo templates.

---

## 🎯 Our Approach

**You (Walter):** Request changes and test results

**Me (Claude):** Edit files directly, commit to git

**Result:** Version-controlled, update-safe customizations

---

## 📋 Standard Workflow

### Step 1: You Request a Change

**Examples:**
- "Add a dark mode toggle to the navigation"
- "Change the header background to a gradient"
- "Add social media icons to the footer"
- "Modify the purchased template's sidebar layout"

### Step 2: I Make the Change

**I will:**

1. **Edit _data/ XML files directly** (for modifications/templates/styles)
2. **Create new PHP files if needed** (for controllers, helpers, etc.)
3. **Commit changes to git** with descriptive message
4. **Tell you what to rebuild** to apply changes

### Step 3: You Apply Changes

**You run:**
```bash
# Pull latest changes (if not on the same session)
cd /home/walter/dev-stack/web-data/dimagriredacampioni.com/comunita/src/addons/DDC/Theme
git pull

# Rebuild to import changes from _data/ to database
docker exec dev-php-fpm php /var/www/dimagriredacampioni.com/comunita/cmd.php xf-addon:rebuild

# If template changes, also rebuild templates
docker exec dev-php-fpm php /var/www/dimagriredacampioni.com/comunita/cmd.php xf:rebuild-templates
```

### Step 4: You Test and Provide Feedback

**You:**
- Browse the site to see changes
- Provide feedback ("looks good" or "needs adjustment")
- Request refinements if needed

### Step 5: We Iterate

Repeat steps 1-4 until perfect, then deploy to production.

---

## 🔧 Types of Changes I'll Make

### 1. Template Modifications (Most Common)

**What:** Search/replace operations on existing templates

**File:** `_data/template_modifications.xml`

**When to use:**
- Modifying purchased template layouts
- Adding elements to existing templates
- Changing text or classes
- Inserting custom HTML

**Example:**
```xml
<modification key="ddc_nav_banner" template="public:navigation"
              modification_key="ddc_nav_banner"
              description="Add announcement banner above navigation"
              execution_order="10" enabled="1" action="str_replace">
    <find><![CDATA[<div class="p-nav">]]></find>
    <replace><![CDATA[<div class="announcement-banner">Site maintenance this weekend</div>
<div class="p-nav">]]></replace>
</modification>
```

### 2. Custom Templates

**What:** Brand new templates you can include anywhere

**File:** `_data/templates.xml`

**When to use:**
- Custom footers, headers, widgets
- Reusable components
- Custom pages

**Example:**
```xml
<template type="public" title="ddc_social_footer" version_id="1000000" version_string="1.0.0">
    <![CDATA[
<div class="ddc-social-footer">
    <a href="https://facebook.com/yourpage" target="_blank">
        <i class="fab fa-facebook"></i> Facebook
    </a>
    <a href="https://twitter.com/yourpage" target="_blank">
        <i class="fab fa-twitter"></i> Twitter
    </a>
</div>
    ]]>
</template>
```

Then include it: `<xf:include template="ddc_social_footer" />`

### 3. Style Properties

**What:** CSS variables, colors, fonts, spacing

**File:** `_data/style_properties.xml`

**When to use:**
- Changing color schemes
- Adjusting spacing/margins
- Font customizations

**Example:**
```xml
<property property_name="primaryColor" group_name="basic" css_components="public:color_palette.less">
    <value_parameters>
        <value><![CDATA[#667eea]]></value>
    </value_parameters>
</property>
```

### 4. Custom CSS/LESS

**What:** Additional stylesheets

**File:** `_data/templates.xml` (as `*.less` templates)

**When to use:**
- Complex custom styling
- Responsive design tweaks
- Custom animations

**Example:**
```xml
<template type="public" title="ddc_custom.less" version_id="1000000" version_string="1.0.0">
    <![CDATA[
.announcement-banner {
    background: linear-gradient(to right, #667eea, #764ba2);
    color: white;
    padding: 10px;
    text-align: center;
    font-weight: bold;
}

.ddc-social-footer {
    display: flex;
    justify-content: center;
    gap: 20px;
    padding: 20px;
    background: #f5f5f5;

    a {
        color: #333;
        text-decoration: none;

        &:hover {
            color: #667eea;
        }
    }
}
    ]]>
</template>
```

### 5. PHP Code (If Needed)

**What:** Custom controllers, helpers, entities

**Files:** Direct PHP files in addon structure

**When to use:**
- Custom functionality beyond templates
- API endpoints
- Data processing

**Example structure:**
```
DDC/Theme/
├── Pub/
│   └── Controller/
│       └── CustomPage.php
└── Helper/
    └── ColorScheme.php
```

---

## 🚀 Rebuild Commands Reference

After I make changes, you'll need to rebuild. Here's what to run:

### Template Modifications or Templates Changed
```bash
docker exec dev-php-fpm php /var/www/dimagriredacampioni.com/comunita/cmd.php xf-addon:rebuild
docker exec dev-php-fpm php /var/www/dimagriredacampioni.com/comunita/cmd.php xf:rebuild-templates
```

### Style Properties Changed
```bash
docker exec dev-php-fpm php /var/www/dimagriredacampioni.com/comunita/cmd.php xf-addon:rebuild
docker exec dev-php-fpm php /var/www/dimagriredacampioni.com/comunita/cmd.php xf:rebuild-css
```

### PHP Code Changed
```bash
docker exec dev-php-fpm php /var/www/dimagriredacampioni.com/comunita/cmd.php xf-addon:rebuild
# May need to clear cache
docker exec dev-php-fpm php /var/www/dimagriredacampioni.com/comunita/cmd.php xf:rebuild-cache
```

### Full Rebuild (When Unsure)
```bash
docker exec dev-php-fpm php /var/www/dimagriredacampioni.com/comunita/cmd.php xf-addon:rebuild
docker exec dev-php-fpm php /var/www/dimagriredacampioni.com/comunita/cmd.php xf:rebuild-templates
docker exec dev-php-fpm php /var/www/dimagriredacampioni.com/comunita/cmd.php xf:rebuild-css
docker exec dev-php-fpm php /var/www/dimagriredacampioni.com/comunita/cmd.php xf:rebuild-cache
```

**Shortcut alias suggestion:**
```bash
# Add to ~/.bashrc or ~/.zshrc
alias xf-rebuild='docker exec dev-php-fpm php /var/www/dimagriredacampioni.com/comunita/cmd.php xf-addon:rebuild && \
docker exec dev-php-fpm php /var/www/dimagriredacampioni.com/comunita/cmd.php xf:rebuild-templates && \
docker exec dev-php-fpm php /var/www/dimagriredacampioni.com/comunita/cmd.php xf:rebuild-css'
```

Then just run: `xf-rebuild`

---

## 📝 Example: Full Workflow

### Your Request
"Add a dark mode toggle button to the navigation menu"

### My Response

**1. Create custom template for toggle button:**
```xml
<!-- In _data/templates.xml -->
<template type="public" title="ddc_dark_mode_toggle">
    <![CDATA[
<button class="ddc-dark-mode-toggle" onclick="toggleDarkMode()">
    <i class="fas fa-moon"></i> Dark Mode
</button>
    ]]>
</template>
```

**2. Modify navigation to include toggle:**
```xml
<!-- In _data/template_modifications.xml -->
<modification key="ddc_nav_dark_mode" template="public:navigation">
    <find><![CDATA[<div class="p-nav-opposite">]]></find>
    <replace><![CDATA[<div class="p-nav-opposite">
<xf:include template="ddc_dark_mode_toggle" />]]></replace>
</modification>
```

**3. Add styling:**
```xml
<!-- In _data/templates.xml -->
<template type="public" title="ddc_custom.less">
    <![CDATA[
.ddc-dark-mode-toggle {
    background: transparent;
    border: 1px solid #ccc;
    padding: 8px 15px;
    border-radius: 4px;
    cursor: pointer;

    &:hover {
        background: #f0f0f0;
    }
}
    ]]>
</template>
```

**4. Commit to git:**
```bash
cd /home/walter/.../DDC/Theme
git add _data/
git commit -m "Add: Dark mode toggle to navigation

- Creates toggle button template
- Modifies navigation to include toggle
- Adds styling for toggle button"
git push
```

**5. Tell you to rebuild:**
```bash
# Run these commands:
docker exec dev-php-fpm php /var/www/dimagriredacampioni.com/comunita/cmd.php xf-addon:rebuild
docker exec dev-php-fpm php /var/www/dimagriredacampioni.com/comunita/cmd.php xf:rebuild-templates
```

**6. You test and provide feedback:**
"Looks great! Can we make it bigger?"

**7. I adjust and commit:**
```xml
<!-- Update padding in ddc_custom.less -->
padding: 12px 20px;  /* Changed from 8px 15px */
font-size: 16px;
```

**8. You rebuild and confirm:**
"Perfect!"

---

## 🎨 Working with Purchased Templates

### When You Buy a Template

**You:** "I bought Xentre from ThemeHouse, here's what I want to customize..."

**I'll:**
1. Ask you which templates/areas to modify
2. Examine the template structure (you can provide screenshots or template names)
3. Create modifications targeting the purchased template's templates
4. All modifications go in DDC/Theme (not vendor code)

**Example - Customizing Xentre header:**

```xml
<modification key="ddc_xentre_header_custom" template="public:th_xentre_header">
    <find><![CDATA[<div class="th-headerBar">]]></find>
    <replace><![CDATA[<div class="th-headerBar ddc-custom-header">]]></replace>
</modification>
```

**Benefits:**
- Xentre can update, your customizations remain
- Clean separation of vendor code and your code
- All your changes version controlled

---

## 🔍 Finding Template Names

### If You Need to Find Template Names

**Option 1: Template Search in Admin CP**
- Go to: Appearance → Templates → Search
- Search for keywords (e.g., "header", "footer", "sidebar")
- Note the template name (e.g., `public:th_xentre_header`)

**Option 2: Enable Debug Mode**
- Add to config.php: `$config['debug'] = 1;`
- Templates will show name in HTML comments
- Look in browser's view source

**Option 3: Ask Vendor**
- Check vendor's documentation
- Most themes document their template structure

**Then tell me:** "The header template is called `public:th_xentre_header`"

---

## ⚙️ Development Mode

### Do We Need It?

**Not strictly required** since I'm editing files directly, but **still useful** for:

1. **Validation**: XenForo validates your addon structure
2. **Auto-sync**: If you ever use Admin CP, changes export correctly
3. **Debugging**: Better error messages
4. **Template hints**: Shows template names in debug mode

**Current setting in your config.php:**
```php
$config['development']['enabled'] = true;
$config['development']['defaultAddOn'] = 'DDC/Theme';
```

**Keep it this way in dev environment.**

---

## 🚀 Deployment to Production

### When Ready to Deploy

**1. Ensure all changes committed:**
```bash
cd /home/walter/.../DDC/Theme
git status  # Should be clean
```

**2. On production:**
```bash
cd /path/to/production/xenforo/src/addons/DDC/Theme
git pull origin main

cd /path/to/production/xenforo
php cmd.php xf-addon:rebuild
php cmd.php xf:rebuild-templates
php cmd.php xf:rebuild-css
```

**3. Verify:**
- Check site looks correct
- Test functionality
- Check error logs

---

## 📊 Summary

| Step | You | Me |
|------|-----|-----|
| **1. Request** | "Add dark mode toggle" | - |
| **2. Implement** | - | Edit XML/PHP files, commit to git |
| **3. Apply** | Run rebuild commands | - |
| **4. Test** | Browse site, provide feedback | - |
| **5. Refine** | "Make it bigger" | Adjust and commit |
| **6. Repeat** | Rebuild, test again | - |
| **7. Deploy** | Pull on production, rebuild | - |

---

## 🆘 Troubleshooting

### Changes Don't Appear After Rebuild

**Check:**
1. Did rebuild commands complete successfully?
2. Any errors in XenForo error log?
3. Try hard refresh in browser (Ctrl+F5)
4. Check file permissions (should be readable)

**Fix:**
```bash
# Full rebuild
docker exec dev-php-fpm php /var/www/dimagriredacampioni.com/comunita/cmd.php xf-addon:rebuild
docker exec dev-php-fpm php /var/www/dimagriredacampioni.com/comunita/cmd.php xf:rebuild-templates
docker exec dev-php-fpm php /var/www/dimagriredacampioni.com/comunita/cmd.php xf:rebuild-cache
```

### Git Conflicts

**Cause:** Multiple sessions editing same files

**Fix:**
```bash
# Stash local changes
git stash

# Pull latest
git pull

# Re-apply changes
git stash pop

# Resolve conflicts if any
# Then commit
```

---

**Ready to start customizing! Just tell me what you'd like to change.** 🎨
