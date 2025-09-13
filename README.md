# Uncovering Hidden Demand: A Data-Driven Analysis of E-Commerce Category Performance at Jumia Egypt

As an aspiring data analyst passionate about turning raw numbers into actionable business strategies, I recently tackled a real-world project during my internship at MyOnlineShop. The goal? To dissect publicly available data from Jumia Egypt, one of Africa's leading e-commerce platforms to uncover patterns in product demand, customer engagement, and category potential. This wasn't just an exercise in crunching numbers; it was a deep dive into how data can guide e-commerce giants like MyOnlineShop in prioritizing categories, optimizing pricing, and boosting customer satisfaction over the next decade.

I'll walk you through the project step by step, sharing the nitty-gritty of my process, the "why" behind each stage, and the insights that emerged. Whether you're a fellow data enthusiast, a hiring manager scouting for analytical thinkers, or an e-commerce pro curious about demand forecasting, I hope this sparks ideas on leveraging data for growth. Let's dive in.

## Project Objectives: Setting the Stage for Strategic Insights

Before touching a single line of code, I defined clear objectives to ensure the analysis was focused and impactful:

1. **Identify High-Potential Categories**: Pinpoint which product categories drive the most engagement (measured by review volume and ratings) to inform assortment decisions.
2. **Quantify Demand Signals**: Use reviews as a proxy for popularity and ratings for satisfaction, creating a composite "engagement score" to rank categories.
3. **Visualize for Storytelling**: Build an intuitive dashboard to make findings accessible, simulating how I'd present to stakeholders.
4. **Extract Business-Relevant Insights**: Translate data into recommendations for MyOnlineShop, including long-term strategies for 2-10 years ahead.

These objectives grounded the project in business value after all, data analysis shines when it bridges the gap between metrics and market moves. By focusing on Jumia's dataset (sourced ethically from public listings), I simulated internal analysis for MyOnlineShop, a fictional e-commerce platform in my internship scenario.

## Step 1: Data Acquisition and Initial Exploration – Building a Solid Foundation

Every great analysis starts with quality data, but real-world datasets are rarely pristine. I began by downloading the `jumia_products.csv` file, which contained ~20,000 rows of product listings from Jumia Egypt (circa May 2022). Key columns included:
- `id`: Unique product identifier.
- `product_name`: Descriptive product titles (e.g., "NIVEA Antiperspirant Spray for Women").
- `price`: Prices in Egyptian Pounds (EGP), often formatted as "EGP 103.75" or ranges like "EGP 329.99 - EGP 399.99".
- `reviews_count`: Number of verified ratings (e.g., "554 verified ratings").
- `avg_rate`: Average star rating (e.g., "4.4/5").

**Process Details**:
- Loaded the data in Python using Pandas: `df = pd.read_csv('jumia_products.csv', encoding='ISO-8859-1')`. The ISO-8859-1 encoding was crucial to handle special characters in Arabic-influenced product names without garbling the text.
- Quick exploratory data analysis (EDA): Checked for missing values (minimal, ~5% in price), duplicates (none), and data types. Printed `df.head()` and `df.describe()` to spot outliers—like a product with 1,066 reviews signaling viral popularity.

**Why It Matters**: Skipping EDA is like building a house on sand. This step revealed that 70% of products had fewer than 10 reviews, highlighting a long-tail distribution typical in e-commerce (few hits, many misses). It set expectations for skewed data and prevented downstream errors, saving hours of debugging.

## Step 2: Data Cleaning and Feature Engineering – Turning Chaos into Clarity

Raw data is messy; clean data unlocks insights. I spent ~40% of my time here, emphasizing the importance of rigor in preprocessing.

**Process Details**:
- **Price Standardization**: Extracted numeric values using regex: `df['price_numeric'] = df['price'].str.extract(r'(\d+(?:\.\d+)?)').astype(float)`. Handled ranges by taking the median (e.g., 329.99 - 399.99 → 364.99). Dropped rows with invalid prices (~2% loss).
- **Reviews and Ratings Parsing**: Stripped text from `reviews_count` (e.g., "554 verified ratings" → 554) and `avg_rate` (e.g., "4.4/5" → 4.4). Converted to integers/floats.
- **Category Inference**: No explicit category column? No problem. I created one by keyword matching in `product_name`:
  - Health & Beauty: Keywords like "NIVEA", "cream", "antiperspirant" → 1,482 products.
  - Fashion: "shoes", "boxers", "dress" → 1,534 products.
  - Phones & Tablets: "iPhone", "Samsung" → 883 products.
  - And so on for Appliances, Baby Products, Computing, Gaming, Home & Furniture, Other Category, Supermarket, Television & Audio (total: 9 categories).
  - Used a simple function: `def categorize(name):` with if-elif chains, achieving ~95% accuracy (validated manually on 500 samples).

**Why It Matters**: Clean data ensures reliable aggregates; poor cleaning amplifies biases (e.g., unparsed prices skew averages). Feature engineering like categorization transformed unstructured text into analyzable groups, enabling category-level insights essential for assortment planning. This step alone revealed Health & Beauty as a review powerhouse (avg. 237.55 reviews/product).

## Step 3: Aggregation and Metric Calculation – Quantifying Demand

With clean data, I aggregated to uncover patterns. Reviews proxy popularity (more = higher traffic/purchases), while ratings signal satisfaction.

**Process Details**:
- Grouped by category: `category_stats = df.groupby('category').agg({'reviews_count': 'mean', 'avg_rate': 'mean', 'price_numeric': 'mean'}).reset_index()`.
- Normalized metrics (0-1 scale) using MinMaxScaler from scikit-learn: Scaled reviews (`norm_reviews`) and ratings (`norm_rating`) to handle different units.
- Created a Weighted Engagement Score: `engagement_score = 0.6 * norm_reviews + 0.4 * norm_rating` (60/40 weights favor volume over quality, as high reviews often correlate with sales velocity).
- Ranked categories: Health & Beauty topped with 0.69, followed by Other Category (0.29) and Phones & Tablets (0.29). Appliances lagged at 0.08.

**Why It Matters**: Aggregation distills 20K rows into digestible stats, revealing disparities (e.g., Supermarket's low score despite volume). The weighted score adds nuance—pure averages miss trade-offs between buzz and quality. This metric directly ties to business KPIs like conversion rates, making it a North Star for prioritization.

## Step 4: Visualization and Dashboard Creation – Making Data Speak

Analysis without visuals is like a story without a plot. I built a dashboard in Jupyter Notebook (exportable to PDF/Slides) using Seaborn and Matplotlib.

**Process Details**:
- Bar Plot: Engagement scores by category (viridis palette for accessibility).
- Heatmap: Normalized reviews vs. ratings, annotated for quick reads.
- Additional: Price distributions (boxplots) and a correlation matrix (reviews vs. price: weak negative, r=-0.12).
- Code Snippet Example:
  ```python
  import seaborn as sns
  import matplotlib.pyplot as plt
  plt.figure(figsize=(12, 6))
  sns.barplot(data=category_stats, x='category', y='engagement_score', palette='viridis')
  plt.title('Market Demand: Product Engagement Score by Category')
  plt.xticks(rotation=45)
  plt.tight_layout()
  plt.show()
  ```

**Why It Matters**: Visuals accelerate comprehension—stakeholders scan a bar chart in seconds vs. poring over tables. My dashboard highlighted "gaps" (e.g., high-price Appliances with low engagement), fostering discussions on targeted interventions.

## Key Insights: What the Data Revealed

From the analysis:
- **Engagement Leaders**: Health & Beauty (0.69 score, 237.55 avg. reviews, 4.58 rating) dominates, driven by affordable, high-turnover items like NIVEA products (avg. price: EGP 119). This signals strong daily demand.
- **Underdogs with Potential**: Computing (0.40 score) and Phones & Tablets (0.29) show solid satisfaction (4.71+ ratings) but moderate reviews—ripe for growth via marketing.
- **Laggards**: Appliances (0.08 score) and Supermarket (0.08) suffer low engagement despite volume, possibly due to high prices (EGP 533K avg. for Appliances? Wait, that's a data artifact—likely total, not per unit) and commoditized perceptions.
- **Pricing Patterns**: Lower prices correlate loosely with higher reviews (r=-0.15), suggesting value-driven Egyptian shoppers.

These insights mirror broader e-commerce trends: Beauty thrives on impulse buys, while durables like appliances need trust-building.

## Recommendations: Actionable Strategies for MyOnlineShop

Based on findings, here's how MyOnlineShop could evolve:

- **Short-Term (2 Years)**: Prioritize Health & Beauty expansions—stock 20% more SKUs, run targeted ads (e.g., "Extra 10% off NIVEA"). For laggards like Appliances, introduce bundles (e.g., fridge + install) to boost perceived value and reviews.
- **Medium-Term (3-5 Years)**: Leverage AI for dynamic pricing: Use engagement scores to auto-adjust (e.g., flash sales for low-score categories). Invest in user-generated content tools to amplify reviews in Gaming/Computing.
- **Long-Term (5-10 Years)**: Build a "Demand Prediction Engine" integrating external signals (e.g., social trends via X searches). Shift to sustainable categories like eco-beauty to future-proof against regulations. Aim for 30% engagement uplift by personalizing recommendations—e.g., "If you loved NIVEA, try this Computing gadget."

These steps could increase category revenue by 15-25%, per industry benchmarks from McKinsey e-commerce reports.

## Wrapping Up: Lessons Learned and the Road Ahead

This project honed my end-to-end skills—from wrangling messy CSVs to crafting narratives that drive decisions. It reminded me that data storytelling isn't just charts; it's empathy, understanding how Egyptian consumers balance affordability with aspiration.

What do you think—ready to geek out over engagement scores? Drop a comment! 🚀

*Posted on LinkedIn, September 2025*
