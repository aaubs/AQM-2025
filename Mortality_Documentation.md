# Global Mortality Enhanced Dataset - Variable Documentation

## Dataset Overview

**File:** `mortality_enhanced.csv`  
**Observations:** 6,156  
**Countries/Entities:** 228 (195 individual countries + 33 regional aggregates)  
**Time Period:** 1990-2016  
**Frequency:** Annual

## About This Dataset

This dataset combines the Global Burden of Disease mortality data from Our World in Data with economic and health indicators from the World Bank. It provides comprehensive data on causes of death as percentages of total mortality, alongside socioeconomic and health system indicators.

**Important Note on Regional Aggregates:**  
The dataset includes both individual countries (n=195) and regional aggregates (n=33) such as "Sub-Saharan Africa" and "High-income countries". Use the `is_aggregate` variable to filter for country-only analysis.

**Important Note on World Bank Data:**  
Economic indicators are annual values matched to mortality data by country and year. 82.9% of observations successfully merged with World Bank data. Missing values occur primarily for regional aggregates and countries with limited statistical capacity.

## Variable Definitions

### Identification Variables

**country**  
Country or region name. String. Examples: "United States", "China", "Sub-Saharan Africa". Not standardized across all sources - see country_code for unique identification.

**country_code**  
Three-letter country code where available. String. Examples: USA, CHN, DEU. May be missing for some regional aggregates. Not fully standardized to ISO 3166-1 alpha-3.

**year**  
Year of observation (1990-2016). Integer. Used for merging with World Bank data.

**decade**  
Decade grouping: 1990, 2000, or 2010. Integer. Useful for long-term trend analysis. Calculated as: `(year // 10) * 10`

**continent**  
Geographic continent for individual countries. Categorical. Values: Africa, Asia, Europe, North America, South America, Central America & Caribbean, Oceania. Missing: 18.4% (1,134 observations) - primarily regional aggregates. String.

**income_group**  
World Bank income classification (2024 thresholds). Categorical. Values:
- **High income**: GDP per capita ≥ $13,845 (e.g., USA, Germany, Japan)
- **Upper middle income**: $4,516-$13,844 (e.g., China, Brazil, Mexico)
- **Lower middle income**: $1,186-$4,515 (e.g., India, Indonesia, Vietnam)
- **Low income**: < $1,186 (e.g., Chad, Malawi, Mozambique)

Missing: 19.4% (1,192 observations) - entities without GDP data. String.

**is_aggregate**  
Indicator for regional aggregates vs individual countries. Boolean. Values: True = regional aggregate, False = individual country. Use to filter: `df[~df['is_aggregate']]` for country-only analysis. No missing values.

### Economic Indicators (World Bank)

**gdp_per_capita** ⭐ **Primary economic predictor**  
Gross Domestic Product per capita in current USD. Float. Range: approximately $100-$110,000. Source: World Bank (NY.GDP.PCAP.CD). Represents average income/wealth per person. Missing: 19.4% (1,192 observations).

**log_gdp_per_capita**  
Natural logarithm of GDP per capita. Float. Use this in regression analyses instead of `gdp_per_capita` to handle skewness and improve model fit. Missing: 19.4% (1,192 observations).

**health_expend_per_cap**  
Health expenditure per capita in current USD. Float. Range: approximately $5-$10,000. Source: World Bank (SH.XPD.CHEX.PC.CD). Total health spending (public + private) per person. Missing: 50.7% (3,122 observations) - limited data availability before mid-1990s.

**log_health_expend**  
Natural logarithm of health expenditure per capita. Float. Preferred for regression analysis due to skewness. Missing: 50.7% (3,122 observations).

**health_expend_pct_gdp**  
Health expenditure as percentage of GDP. Float. Range: approximately 2%-18%. Source: World Bank (SH.XPD.CHEX.GD.ZS). Indicates healthcare system investment relative to economy size. Missing: 50.7% (3,122 observations).

### Demographic Indicators (World Bank)

**urban_pct**  
Percentage of population living in urban areas. Float. Range: approximately 5%-100%. Source: World Bank (SP.URB.TOTL.IN.ZS). Proxy for development level and healthcare access. Missing: 17.1% (1,053 observations).

**pop_65plus_pct**  
Percentage of population aged 65 and above. Float. Range: approximately 1%-25%. Source: World Bank (SP.POP.65UP.TO.ZS). Key demographic indicator for age-related mortality patterns. Missing: 17.1% (1,053 observations).

**fertility_rate**  
Total fertility rate (births per woman). Float. Range: approximately 1-8 children per woman. Source: World Bank (SP.DYN.TFRT.IN). Indicator of demographic transition stage. Missing: 17.1% (1,053 observations).

### Health Infrastructure Indicators (World Bank)

**safe_water_pct**  
Percentage of population with access to safely managed drinking water. Float. Range: approximately 5%-100%. Source: World Bank (SH.H2O.SMDW.ZS). Critical for communicable disease control. Missing: 66.0% (4,063 observations) - highly limited data availability.

**basic_sanitation_pct**  
Percentage of population with access to basic sanitation services. Float. Range: approximately 5%-100%. Source: World Bank (SH.STA.BASS.ZS). Critical for communicable disease control. Missing: 48.5% (2,987 observations) - limited data availability.

### Aggregate Disease Categories

**communicable_diseases_total**  
Sum of mortality percentages from communicable diseases. Float. Includes: Lower respiratory infections, Diarrheal diseases, Tuberculosis, HIV/AIDS, Malaria, Meningitis, Intestinal infectious diseases, Hepatitis. Calculated as sum of component percentages.

**non_communicable_diseases_total**  
Sum of mortality percentages from non-communicable diseases. Float. Includes: Cardiovascular diseases, Cancers, Respiratory diseases, Diabetes, Dementia, Liver disease, Kidney disease, Digestive diseases, Parkinson disease. Calculated as sum of component percentages.

**injuries_total**  
Sum of mortality percentages from injuries. Float. Includes: Road accidents, Suicide, Homicide, Drowning, Fire, Natural disasters, Conflict, Terrorism. Calculated as sum of component percentages.

**maternal_child_total**  
Sum of mortality percentages from maternal and child health causes. Float. Includes: Neonatal deaths, Maternal deaths, Nutritional deficiencies, Protein-energy malnutrition. Calculated as sum of component percentages.

### Individual Cause of Death Variables (%) ⭐ **Primary outcome variables**

All cause-specific mortality variables represent the percentage of total deaths attributable to that cause in a given country-year. Float. Range: 0-100%. Source: Global Burden of Disease via Our World in Data.

**Major Non-Communicable Diseases:**

**Cardiovascular diseases (%)**  
Deaths from heart disease and stroke. Float. Typically 20-50% in developed countries, lower in developing countries.

**Cancers (%)**  
Deaths from all malignant neoplasms. Float. Typically 10-30% across countries.

**Respiratory diseases (%)**  
Deaths from chronic respiratory diseases (COPD, asthma). Float. Typically 2-8%.

**Diabetes (%)**  
Deaths from diabetes mellitus. Float. Typically 1-5%.

**Dementia (%)**  
Deaths from Alzheimer's and other dementias. Float. Typically 1-10%, higher in aged populations.

**Liver disease (%)**  
Deaths from cirrhosis and chronic liver diseases. Float. Typically 1-5%.

**Kidney disease (%)**  
Deaths from chronic kidney disease. Float. Typically 1-3%.

**Digestive diseases (%)**  
Deaths from digestive system diseases. Float. Typically 1-5%.

**Parkinson disease (%)**  
Deaths from Parkinson's disease. Float. Typically <1-2%.

**Major Communicable Diseases:**

**Lower respiratory infections (%)**  
Deaths from pneumonia and other lower respiratory infections. Float. Higher in low-income countries (5-15%) than high-income (<5%).

**Diarrheal diseases (%)**  
Deaths from diarrheal diseases. Float. Primarily affects low-income countries (1-10%).

**Tuberculosis (%)**  
Deaths from TB. Float. Higher in low-income countries (1-8%).

**HIV/AIDS (%)**  
Deaths from HIV/AIDS. Float. Highly variable by region; epidemic levels in Sub-Saharan Africa (up to 30%).

**Malaria (%)**  
Deaths from malaria. Float. Concentrated in Sub-Saharan Africa (up to 15%).

**Meningitis (%)**  
Deaths from meningitis. Float. Typically <1-2%.

**Intestinal infectious diseases (%)**  
Deaths from intestinal infections. Float. Typically <1-2%.

**Hepatitis (%)**  
Deaths from viral hepatitis. Float. Typically <1-2%.

**Maternal and Child Health:**

**Neonatal deaths (%)**  
Deaths in first 28 days of life. Float. Higher in low-income countries (5-15%).

**Maternal deaths (%)**  
Deaths from pregnancy and childbirth complications. Float. Higher in low-income countries (0.5-2%).

**Nutritional deficiencies (%)**  
Deaths from malnutrition and vitamin deficiencies. Float. Primarily low-income countries (0.5-3%).

**Protein-energy malnutrition (%)**  
Deaths specifically from protein-energy malnutrition. Float. Subset of nutritional deficiencies.

**Injuries:**

**Road accidents (%)**  
Deaths from road traffic injuries. Float. Typically 1-5%.

**Suicide (%)**  
Deaths from intentional self-harm. Float. Typically 1-3%.

**Homicide (%)**  
Deaths from interpersonal violence. Float. Highly variable (0.2-10%).

**Drowning (%)**  
Deaths from drowning. Float. Typically <1-2%.

**Fire (%)**  
Deaths from fire and heat. Float. Typically <1%.

**Natural disasters (%)**  
Deaths from earthquakes, floods, storms. Float. Typically <0.5%, episodic.

**Conflict (%)**  
Deaths from armed conflict. Float. Typically <1%, episodic and regional.

**Terrorism (%)**  
Deaths from terrorism. Float. Typically <0.5%, episodic and regional.

**Other Causes:**

**Alcohol disorders (%)**  
Deaths from alcohol use disorders. Float. Typically <1%.

**Drug disorders (%)**  
Deaths from drug use disorders. Float. Typically <1%.

**Heat-related (hot and cold exposure) (%)**  
Deaths from temperature extremes. Float. Typically <0.5%.

## Missing Data Summary

| Variable | Missing Count | Missing % |
|----------|--------------|-----------|
| safe_water_pct | 4,063 | 66.0% |
| health_expend_per_cap | 3,122 | 50.7% |
| health_expend_pct_gdp | 3,122 | 50.7% |
| basic_sanitation_pct | 2,987 | 48.5% |
| gdp_per_capita | 1,192 | 19.4% |
| income_group | 1,192 | 19.4% |
| continent | 1,134 | 18.4% |
| fertility_rate | 1,053 | 17.1% |
| urban_pct | 1,053 | 17.1% |
| pop_65plus_pct | 1,053 | 17.1% |

Missing data is primarily from:
- Regional aggregates (no World Bank country-level data)
- Developing countries with limited statistical capacity
- Water/sanitation data (limited global coverage before 2000)
- Health expenditure data (limited coverage before mid-1990s)

## Key Variables for Analysis

**Dependent Variables (outcomes):**
- Individual cause-specific mortality percentages (32 variables)
- Aggregate disease categories: `communicable_diseases_total`, `non_communicable_diseases_total`, `injuries_total`, `maternal_child_total`

**Independent Variables (predictors):**
- `log_gdp_per_capita`: GDP per capita (log scale) - primary economic predictor
- `log_health_expend`: Health expenditure per capita (log scale)
- `health_expend_pct_gdp`: Health system investment
- `urban_pct`: Urbanization level
- `pop_65plus_pct`: Population aging
- `fertility_rate`: Demographic transition stage
- `safe_water_pct`, `basic_sanitation_pct`: Basic infrastructure

**Grouping Variables:**
- `continent`: Regional grouping
- `income_group`: Income classification
- `decade`: Long-term trends
- `is_aggregate`: Filter for country-level analysis

## Data Structure Notes

**Panel Structure:**  
Same countries appear 27 times (1990-2016). Consider using country fixed effects or clustered standard errors in regression analyses.

**Regional Aggregates:**  
33 entities are regional/income aggregates. Filter using: `df[~df['is_aggregate']]` for country-only analysis (n=5,535).

**Successful Merge Rate:**  
82.9% of observations (5,103/6,156) successfully merged with World Bank data. 17.1% are left-only (mortality data without economic indicators).

**Epidemiological Transition:**  
Dataset captures the global shift from communicable to non-communicable diseases. Low-income countries show high communicable disease burden; high-income countries show high non-communicable disease burden.

## Data Sources

**Mortality Data:**  
Our World in Data. "Causes of Death." https://ourworldindata.org/causes-of-death  
Original source: Global Burden of Disease Study, Institute for Health Metrics and Evaluation (IHME)

**Economic & Health Indicators:**  
World Bank. "World Development Indicators." https://data.worldbank.org/

**Dataset Compilation:**  
Applied Quantitative Methods Course, Aalborg University Business School, January 2025
