# Product Image Enhancement (Optional Post-Processing)

## Purpose

After a research report is complete, optionally enrich the business sections with product images from the company's official website. This is a cosmetic enhancement — it does NOT alter the report's analysis.

## When to Apply

Apply only when:
- The company produces **physical, visually distinctive products** (manufacturing, consumer goods, hardware, etc.)
- The company's website has an accessible product/brand showcase
- **Skip for:** financial services, pure software/SaaS, holding companies, companies whose products are not visually interesting

## Workflow

### Step 1: Locate Product Images

Use browser tools to navigate the company's official website:

1. **Start at the homepage** — `browser_navigate` to the company website
2. **Find brand/product pages** — look for navigation links like "产品中心", "旗下品牌", "Products", "Brands"
3. **Drill into sub-pages** — iterate through each brand/product category
4. **Extract images** — use `browser_get_images` on each product page
5. **Filter** — ignore logos, icons, banners; keep only actual product photos

### Step 2: Verify Image URLs

Test a few image URLs from each source with `curl -sI` to confirm they return HTTP 200 and are accessible.

### Step 3: Match Images to Report Sections

Map each product image to the corresponding business segment in Section 2 of the report:

| Business segment | Image source |
|-----------------|-------------|
| Core product line | Brand/product page images |
| Industrial/B2B products | May require representative images with attribution |
| New/unlisted products | May not exist on website — skip, don't fabricate |

**Fallback for industrial products:** If the company doesn't showcase a specific product category on its website, you may use:
- Representative images from the same industry (with clear attribution)
- Wikimedia Commons free-license images for context/scene shots
- **Always** label these clearly — never imply they are the company's own photos

### Step 4: Insert into Report

Use the `patch` tool to insert image blocks after the closing text of each business subsection in Section 2. Use HTML `<img>` tags (not Markdown `![]()` syntax) to enable size control.

**Image sizing convention:**

| Layout | Max Width | Use Case |
|--------|----------|----------|
| 280px | 3 images per row | Product gallery (3 variants side by side) |
| 400px | 2 images per row | Pair comparison |
| 480px | Standalone | Single product close-up or scene photo |

**Template:**

```markdown
#### 产品展示

[product group description]：

<img src="IMAGE_URL_1" alt="[alt text]" style="max-width:280px;">
<img src="IMAGE_URL_2" alt="[alt text]" style="max-width:280px;">
<img src="IMAGE_URL_3" alt="[alt text]" style="max-width:280px;">

*图：[caption explaining what these products are and their key features]。*

（图片来源：[source attribution]）
```

**Rules:**
- Place images **immediately** after the last text paragraph of the business subsection, before the next `###` heading
- Always include a `*图：*` italic caption below each image group
- Always include a `（图片来源：...）` attribution line
- For products where only representative images are used, explain clearly in the caption
- Keep the enhancement additive — never modify existing text, only insert new image blocks

## Example

See the 华瓷股份 (001216) report for a complete working example:
- `output/华瓷股份_001216_深度研究报告_2026-04-25_合并版.md`
- 4 business segments enhanced, 12 images total, 3 layout types
