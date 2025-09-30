---
layout: default
title: Documentation Home
nav_order: 2
---

# 📚 Documentation Hub

## 🔗 Quick Links
{% for page in site.pages %}
  {% if page.path contains 'docs/' and page.name != 'README.md' %}
- [{{ page.title | default: page.name }}]({{ page.url | relative_url }})
  {% endif %}
{% endfor %}

## 🏷️ Browse by Tags
- [Networking](/docs/tag/networking)
- [Security](/docs/tag/security) 
- [Configuration](/docs/tag/configuration)

## 📖 How to Use This Docs
1. **Create new notes** in Obsidian as usual
2. **Use wikilinks** for backlinks: `[[Page Name]]`
3. **Add frontmatter** for metadata:
   ```yaml
   ---
   title: "Your Title"
   date: 2025-09-30
   tags: [networking, firewall]
   ---