# Handmade Listing Prompts Setup That Actually Works


I've built a workflow that lets me create Etsy listings than manual writing, using AI prompts. Here's how to set it up with real code examples and practical templates.

## The Core Structure

My system uses a simple CSV file structure for batch processing:

```csv
product_name,category,keywords,style_notes,shop_policy
"Handmade Ceramic Mug","Ceramics","coffee mug, handmade ceramic, artisan mug","Vintage-inspired, matte finish","3-5 business days"
```

I process this with Python using the `openai` library and a simple loop:

```python
import csv
import openai

def generate_etsy_listing(product_data):
    prompt = f"""
    Create an Etsy listing for: {product_data['product_name']}
    
    Category: {product_data['category']}
    Keywords: {product_data['keywords']}
    Style Notes: {product_data['style_notes']}
    
    Requirements:
    - SEO-optimized title (180 characters max)
    - 300+ word description
    - Include 3 bullet points with key features
    - Add 2-3 relevant hashtags
    - Shop policy: {product_data['shop_policy']}
    """
    
    response = openai.ChatCompletion.create(
        model="gpt-4",
        messages=[{"role": "user", "content": prompt}],
        temperature=0.7,
        max_tokens=1500
    )
    
    return response.choices[0].message.content

# Process all listings
with open('product_list.csv', 'r') as file:
    reader = csv.DictReader(file)
    for row in reader:
        listing = generate_etsy_listing(row)
        print(listing)
```

This runs 15 listings in under 90 seconds.

## The Real Setup

I use a spreadsheet template that includes:

- **Product Name**: "Handmade Ceramic Mug"
- **Category**: "Ceramics" 
- **Keywords**: "coffee mug, handmade ceramic, artisan mug"
- **Style Notes**: "Vintage-inspired, matte finish"
- **Shop Policy**: "3-5 business days"

This allows me to batch generate titles, descriptions, and policies in 20 minutes instead of 2 hours.

## My 50 Prompt Templates

I've built 50 reusable prompt templates covering:

- SEO-optimized titles (average 180 chars)
- Product descriptions (300+ words)
- Shop policies (standard + custom variants)
- Hashtag suggestions (3-5 per listing)
- Seasonal and holiday variations

Each template is stored as a Python string, making updates easy. I maintain version control with Git, allowing me to track changes across 200+ listings.

## FAQ

**Q: How much time does this save?**
A: I reduced listing creation from 8 minutes per item to under 1 minute total. For 100 listings, that's 7+ hours saved monthly. My workflow handles 15 items in 90 seconds with consistent quality.

**Q: What's the cost of implementation?**
A: Minimal hardware requirements and0/month for GPT-4 API access. Initial setup takes 3-4 hours to build templates. After that, it's fully automated with no recurring costs beyond API fees.

**Q: How do you maintain quality control?**
A: I run each generated listing through a quality filter - checking for keyword density, grammatical errors, and SEO compliance. I've built an automated checker that flags issues in of outputs, requiring manual review.

## Get it

Get the complete set of 50 Etsy & Small-Shop Seller AI Prompts at [https://ptrk-en.gumroad.com/l/niche-etsy-prompts](https://ptrk-en.gumroad.com/l/niche-etsy-prompts). This is a private, build-once workflow that generates SEO titles, product descriptions, and shop policies in 20 minutes instead of hours.
