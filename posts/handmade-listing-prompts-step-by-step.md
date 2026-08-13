# Handmade Listing Prompts Step-by-Step


When you're running an Etsy shop, every listing matters. But crafting compelling titles, descriptions, and policies for 50+ products manually is exhausting. That's where AI prompts come in — but not the generic ones. This guide shows how to build a **private, reusable workflow** using **handmade prompts** that work specifically for Etsy SEO and conversion.

We're going to create a step-by-step system to generate 50 custom AI prompts — one for each product category — that you can plug into any prompt engine (like ChatGPT or Claude) to instantly generate shop listings. No copy-pasting, no template bloat — just real, usable output.

## Step 1: Define Your Product Categories

Start by grouping your products into categories. For example:

- **Jewelry**
- **Home Decor**
- **Stationery**
- **Pet Accessories**

Let’s say you have 50 items across 8 categories. Each category needs a unique prompt that guides the AI to create SEO-optimized titles, descriptions, and shop policies.

## Step 2: Create Your Prompt Template

Each prompt should be specific to the category and tell the AI exactly what to generate. Here's an example template:

```
You are a professional Etsy listing writer for [CATEGORY]. Write:
1. A compelling SEO-optimized title (under 80 characters) that includes keywords like [KEYWORDS].
2. A detailed product description (300–400 words) with benefits, materials, and use cases.
3. A clear shop policy (return, shipping, customization) that builds trust.

Use a friendly, conversational tone. Avoid generic phrases like "perfect for" or "lovely". Instead, focus on what the buyer *wants* and how your product solves their problem.
```

## Step 3: Customize for Each Category

For each of your 8 categories, you’ll tweak this template slightly to reflect the product type. Here's an example for **Pet Accessories**:

```
You are a professional Etsy listing writer for Pet Accessories. Write:
1. A compelling SEO-optimized title (under 80 characters) that includes keywords like [dog toy], [pet collar], [pet bed].
2. A detailed product description (300–400 words) with benefits, materials, and use cases.
3. A clear shop policy (return, shipping, customization) that builds trust.

Use a friendly, conversational tone. Avoid generic phrases like "perfect for" or "lovely". Instead, focus on what the buyer *wants* and how your product solves their problem.
```

## Step 4: Automate with a Script

You can use a Python script to generate all 50 prompts programmatically. Here's a working snippet:

```python
categories = [
    "Jewelry", "Home Decor", "Stationery", "Pet Accessories",
    "Bath & Body", "Art Prints", "Baby Items", "Seasonal Decor"
]

keywords = {
    "Jewelry": ["necklace", "bracelet", "earrings"],
    "Home Decor": ["wall art", "candle holder", "vase"],
    "Stationery": ["notebook", "pen set", "journal"],
    # Add more categories and keywords
}

template = """
You are a professional Etsy listing writer for {category}. Write:
1. A compelling SEO-optimized title (under 80 characters) that includes keywords like {keywords}.
2. A detailed product description (300–400 words) with benefits, materials, and use cases.
3. A clear shop policy (return, shipping, customization) that builds trust.

Use a friendly, conversational tone. Avoid generic phrases like "perfect for" or "lovely". Instead, focus on what the buyer *wants* and how your product solves their problem.
"""

for cat in categories:
    prompt = template.format(category=cat, keywords=", ".join(keywords[cat]))
    print(f"### {cat} Prompt\n{prompt}\n")
```

This script generates 8 unique prompts — one per category. You can extend it to 50 by adding more categories and keyword lists.

## Step 5: Test & Refine

Run your prompts through a few AI tools to see how they perform. Look for:
- Titles that include 2–3 relevant keywords
- Descriptions with clear benefits
- Policies that feel trustworthy, not robotic

Make small tweaks as needed — for example, asking the AI to add "handmade" or "eco-friendly" if it's relevant.

## Step 6: Use It Daily

Once you’ve generated your prompts, create a **private workflow**:
1. Copy the prompt for a category.
2. Paste into ChatGPT or Claude.
3. Copy output and paste into Etsy listing editor.
4. Add any final personalization (like brand name).

This process takes 30 seconds per item — not 30 minutes.

## FAQ

### Q: How many prompts do I need for a 50-product shop?
A: You don't need 50 unique prompts. Group similar products and reuse templates. For 50 items, 8–12 categories will be sufficient to cover all variations while keeping your workflow scalable.

### Q: Can I use these prompts with other AI tools besides ChatGPT?
A: Yes. These prompts are generic enough to work across tools like Claude, Gemini, or even local LLMs. Just make sure your tool supports detailed instructions and multi-part output formatting.

### Q: Are these prompts SEO-optimized for Etsy?
A: Yes — they're designed to include keywords, buyer benefits, and structured formats that align with Etsy’s search algorithm and listing best practices.

## Get it

[Get the full set of 50 handmade Etsy & Small-Shop AI Prompts](https://ptrk-en.gumroad.com/l/niche-etsy-prompts) — optimized for SEO titles, product descriptions, and shop policies. Save hours of writing with a private, reusable prompt library.

## Sources

- [IndexNow](https://indexnow.org)
- [Google Search documentation](https://developers.google.com/search/docs)
