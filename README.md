# DDC Custom Theme

XenForo theme addon for DimagrireDaCampioni custom styling and templates.

## Purpose

This addon contains **YOUR customizations** on top of:
- XenForo default templates
- Purchased/third-party themes
- Any other base theme

**What's included:**
- Template modifications (non-destructive changes to any template)
- Custom template overrides (complete template replacements when needed)
- Custom templates (wholly new templates)
- Style property overrides
- Custom CSS/LESS files
- Branding and visual customizations

**What's NOT included:**
- Purchased template/theme code (vendor code, not version controlled)
- XenForo core templates (maintained by XenForo)
- Third-party addon templates (maintained by addon authors)

## How It Works with Purchased Templates

When you purchase a XenForo theme (e.g., UI.X, Xentre, Flat Awesome, etc.):

1. **Install purchased theme normally** → Separate addon/style, NOT in git
2. **Create modifications via DDC/Theme** → Your customizations, IN git
3. **Template modifications target purchased templates** → Applied on top
4. **Updates work seamlessly** → Vendor updates, your mods re-apply

**Example:**
- Purchased: ThemeHouse Xentre (in `src/addons/ThemeHouse/Xentre/`)
- Customizations: DDC/Theme (in `src/addons/DDC/Theme/`)
- Modification targets: `public:th_xentre_header`, `public:th_xentre_sidebar`, etc.
- Version control: Only DDC/Theme (your changes)

This keeps vendor code separate from your customizations and makes both update-safe.

## Development Workflow

### 1. Enable Development Mode

Add to `src/config.php`:

```php
$config['development']['enabled'] = true;
$config['development']['defaultAddOn'] = 'DDC/Theme';
```

### 2. Make Changes via XenForo Admin CP

**For Template Modifications (preferred):**
1. Go to: Appearance → Template Modifications → Add Template Modification
2. Make your changes (search/replace, add before/after)
3. Changes auto-export to `_data/template_modifications.xml`

**For Full Template Edits (when necessary):**
1. Go to: Appearance → Templates → Search for template
2. Edit template in Admin CP
3. Changes auto-export to `_data/templates.xml`

**For Styles:**
1. Go to: Appearance → Styles → Style Properties
2. Make changes
3. Changes auto-export to `_data/style_properties.xml`

### 3. Commit to Git

```bash
cd /path/to/src/addons/DDC/Theme
git add _data/
git commit -m "Add: Navigation customization"
git push
```

### 4. Deploy to Production

```bash
# On production server
cd /path/to/xenforo/src/addons/DDC/Theme
git pull origin main
cd ../../../
php cmd.php xf:addon-rebuild
```

## Template Modifications vs. Template Edits

### Use Template Modifications When:
- ✅ Adding/removing small elements
- ✅ Changing text/labels
- ✅ Inserting custom HTML in specific locations
- ✅ Adding CSS classes or attributes
- ✅ Changes should survive XenForo updates

### Use Template Edits When:
- ⚠️ Complete layout redesign needed
- ⚠️ Template modifications become too complex
- ⚠️ Creating entirely new custom templates
- ⚠️ May need manual review after XenForo updates

## Best Practices

1. **Prefer Template Modifications** over full template edits
2. **Use descriptive names** for modifications (e.g., "nav_add_chat_link")
3. **Add comments** explaining why the change was made
4. **Test after XenForo updates** to ensure modifications still work
5. **Keep modifications simple** - one logical change per modification

## File Structure

```
_data/
├── templates.xml              # Full template overrides (use sparingly)
├── template_modifications.xml # Search/replace modifications (preferred)
├── styles.xml                 # Custom style definitions
├── style_properties.xml       # Colors, fonts, spacing, etc.
├── navigation.xml             # Custom navigation entries
└── widget_*.xml              # Custom widgets
```

## Common Customization Examples

### Add Custom Navigation Link

**Via Template Modification:**
- Template: `public:navigation`
- Find: `<xf:if is="$xf.visitor.user_id">`
- Replace:
  ```html
  <xf:if is="$xf.visitor.user_id">
      <a href="/chat">Chat</a>
  ```

### Change Footer Text

**Via Template Modification:**
- Template: `public:footer`
- Find: `{$xf.options.boardTitle}`
- Replace: `DimagrireDaCampioni Community`

### Add Custom CSS

**Via Template (create new):**
- Create: `public:extra.less`
- Add custom LESS/CSS code

## Version Control

All changes in `_data/` are automatically tracked by Git when development mode is enabled.

## Troubleshooting

### Changes not appearing in _data/
- Verify development mode is enabled in config.php
- Verify defaultAddOn is set to 'DDC/Theme'
- Rebuild addon: `php cmd.php xf:addon-rebuild`

### Changes not applying on production
- Ensure addon is enabled in Admin CP
- Clear XenForo cache
- Rebuild templates: `php cmd.php xf:rebuild-templates`

### Template modifications break after update
- Check XenForo error logs
- Review modified templates for structural changes
- May need to update find/replace strings
