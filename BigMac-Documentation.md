# Big Mac Enhanced Dataset - Variable Documentation

## Dataset Overview

**File:** `bigmac_enhanced.csv`  
**Observations:** 1,386  
**Countries:** 56  
**Time Period:** 2000-2020  
**Frequency:** Semi-annual (approximately January and July each year)

## About This Dataset

This dataset combines the Big Mac Index from *The Economist* with economic indicators from the World Bank. The Big Mac Index measures whether currencies are over or undervalued by comparing Big Mac prices across countries.

**Important Note on Time Structure:**  
Economic indicators (GDP, inflation, unemployment) are annual values. When there are two Big Mac observations in the same year (e.g., January and July), both receive the same economic indicator values. This is standard practice when merging data with different time frequencies.

---

## Variable Definitions

### Time Variables

**date**  
Date of the Big Mac price survey (YYYY-MM-DD format). Example: 2020-01-14

**year**  
Year of observation (2000-2020). Integer. Used for merging with World Bank data.

**month**  
Month of observation (1-12). Integer.

**semester**  
Half-year indicator: "H1" (January-June) or "H2" (July-December). Categorical.

**quarter**  
Quarter of observation (1, 2, 3, or 4). Integer.

**decade**  
Decade grouping: 2000, 2010, or 2020. Integer. Useful for long-term trend analysis.

---

### Geographic Variables

**iso_a3**  
Three-letter country code following ISO 3166-1 alpha-3 standard. Examples: USA, CHN, DEU, BRA. String. Used as unique country identifier.

**name**  
Full country name. Examples: "United States", "China", "Germany", "Brazil". String.

**currency_code**  
Three-letter local currency code. Examples: USD, CNY, EUR, JPY. String.

---

### Big Mac Price Variables

**local_price**  
Price of a Big Mac in local currency. Float. Examples: 5.67 (USD), 21.90 (CNY), 390.00 (JPY).

**dollar_ex**  
Exchange rate: how many units of local currency equal 1 USD. Float. Examples: USA = 1.0, China = 6.9, Japan = 109.5.

**dollar_price** ⭐ *Primary outcome variable*  
Big Mac price converted to USD using the exchange rate. Float. Range: approximately $1.00-$7.00. Calculated as: local_price / dollar_ex. This is the main variable for comparing prices across countries.

**usd_raw**  
Over/undervaluation index relative to USD. Float. Range: approximately -0.60 to +0.60.  
- Positive = currency overvalued (Big Mac more expensive than expected)
- Negative = currency undervalued (Big Mac cheaper than expected)  
- Zero = fair value (base reference for USD)

**eur_raw, gbp_raw, jpy_raw, cny_raw**  
Over/undervaluation indices relative to Euro, British Pound, Japanese Yen, and Chinese Yuan. Float.

**gdp_dollar**  
GDP-adjusted Big Mac price. Float. May contain missing values.

**adj_price**  
Adjusted price accounting for GDP differences. Float. May contain missing values.

**usd_adjusted, eur_adjusted, gbp_adjusted, jpy_adjusted, cny_adjusted**  
GDP-adjusted over/undervaluation indices for various currencies. Float. May contain missing values.

---

### Economic Indicator Variables (World Bank)

**gdp_per_capita** ⭐ *Primary predictor variable*  
Gross Domestic Product per capita in current USD. Float. Range: approximately $1,000-$90,000. Source: World Bank (NY.GDP.PCAP.CD). Represents average income/wealth per person. Missing: 7.0% (97 observations).

**log_gdp_per_capita**  
Natural logarithm of GDP per capita. Float. Use this in regression analyses instead of gdp_per_capita to handle skewness and improve model fit.

**inflation_rate**  
Annual percentage change in Consumer Price Index. Float. Range: approximately -5% to +50%. Source: World Bank (FP.CPI.TOTL.ZG). Positive values = inflation, negative = deflation. Missing: 9.7% (134 observations).

**unemployment_rate**  
Unemployment as percentage of total labor force. Float. Range: approximately 2%-25%. Source: World Bank (SL.UEM.TOTL.NE.ZS). Missing: 20.7% (287 observations). Note: Highest missingness rate, especially for developing countries.

**population**  
Total population. Float. Range: ~300,000 to 1.4 billion. Source: World Bank (SP.POP.TOTL). Missing: 7.0% (97 observations).

**log_population**  
Natural logarithm of population. Float. Preferred for regression analysis due to the wide range of population values.

**urban_pct**  
Percentage of population living in urban areas. Float. Range: 15%-100%. Source: World Bank (SP.URB.TOTL.IN.ZS). Proxy for development level and cost structure. Missing: 7.0% (97 observations).

---

### Classification Variables

**continent**  
Geographic continent. Categorical. Values: North America, South America, Europe, Asia, Oceania, Africa. No missing values. Use for regional comparisons.

**income_group**  
World Bank income classification (2024). Categorical. Values:
- High income: GDP per capita > $13,845 (e.g., USA, Germany, Japan)
- Upper middle income: $4,516-$13,845 (e.g., China, Brazil, Mexico)
- Lower middle income: $1,186-$4,515 (e.g., India, Indonesia, Vietnam)
- Low income: < $1,186 (none in this dataset)

No missing values. Use for income-level comparisons.

**eu_member**  
European Union membership status (as of 2024). Binary. Values: 1 = EU member, 0 = not EU member. Note: This is a static classification and does not account for countries joining the EU during the 2000-2020 period.

---

## Missing Data Summary

| Variable | Missing Count | Missing % |
|----------|---------------|-----------|
| gdp_per_capita | 97 | 7.0% |
| population | 97 | 7.0% |
| urban_pct | 97 | 7.0% |
| inflation_rate | 134 | 9.7% |
| unemployment_rate | 287 | 20.7% |
| income_group | 0 | 0.0% |
| continent | 0 | 0.0% |

Missing economic data is primarily from:
- Special administrative regions (Hong Kong, Euro area aggregate)
- Developing countries with limited statistical capacity
- Recent years where World Bank data is not yet published

---

## Key Variables for Analysis

**Dependent Variables (outcomes):**
- dollar_price: Big Mac price in USD (main outcome)
- usd_raw: Currency over/undervaluation

**Independent Variables (predictors):**
- log_gdp_per_capita: GDP per capita (log scale) - primary predictor
- inflation_rate: Annual inflation
- unemployment_rate: Unemployment level
- urban_pct: Urbanization level

**Grouping Variables:**
- continent: Regional grouping
- income_group: Income classification
- semester: Within-year timing
- year: Time trend

---

## Usage Notes

1. **Multiple observations per country-year:** Some countries have two observations per year (January and July). Both use the same annual economic indicators.

2. **Regression with GDP:** Always use `log_gdp_per_capita` instead of `gdp_per_capita` in regression models to handle skewness.

3. **Missing data:** When performing analysis, explicitly handle missing values using `.dropna()` and report how many observations were excluded.

4. **Panel structure:** Same countries appear multiple times across years. Consider using country fixed effects or clustered standard errors in regression analyses.

5. **Time-series consideration:** Dollar values are nominal (not inflation-adjusted). Be cautious when comparing prices across years.

---

## Data Sources

**Big Mac Index:**  
The Economist. "The Big Mac Index." https://www.economist.com/big-mac-index

**Economic Indicators:**  
World Bank. "World Development Indicators." https://data.worldbank.org/

**Dataset Compilation:**  
Applied Quantitative Methods Course, Aalborg University Business School, 2025

---
