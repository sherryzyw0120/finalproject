# Final Project Proposal

To: BST260 Teaching Team, Fall 2026

From: Yunwei Zhang (GitHub: sherryzyw0120)

Project site: <https://sherryzyw0120.github.io/finalproject>

## What I plan to do

For my final project, I want to work with two of the datasets that come
with the course package. The first part looks at whether state
background-check laws line up with changes in firearm deaths, using the
RAND gun law data (`gunleg`) together with the CDC firearm mortality
data (`cdcfiremort`). I may also use the CDC homicide data (`cdcmurd`)
as a second outcome. The second part uses the CDC outbreak data
(`waterborne`) to ask whether outbreaks spread in different ways (food,
water, person to person) differ in how often people end up in the
hospital.

Each part will be its own vignette. I’ll explain where the data came
from, show descriptive statistics for the variables I use, state what
I’m trying to find out, and write up what I found and what the
limitations are. Some steps will be repeated a lot (cleaning, joining,
summarizing by state or by year), so I’ll write small R functions for
those instead of copying code.

## Part 1: Background-check laws and firearm deaths

### The data

‘gunleg’ is the RAND State Firearm Law Database, which contains 1,902
rows, with each row representing a different law. For each law, the
dataset includes the state, the type of law (out of 20 categories, such
as background checks, waiting periods, and child access), whether the
law made firearm access more restrictive or permissive, and the dates
when it took effect and, if applicable, was replaced. The effective
dates range from 1813 to 2025.

‘cdcfiremort’ comes from CDC/NCHS state-level statistics. It includes
firearm death rates per 100,000 people and the total number of deaths
for each state and DC. The data cover 2005 and 2014 through 2024, giving
a total of 612 state-year observations. ‘cdcmurd’ has the same structure
but focuses on homicide.

One difference I noticed is that ‘cdcfiremort’ uses full state names,
while ‘cdcmurd’ uses two-letter state abbreviations. Since gunleg
includes both, I can use it to match the states across the datasets.
I’ll also mention the gap between 2005 and 2014 in my write-up, since
the data do not cover those years continuously.

### Research Question

Do firearm death rates change after a state adopts a restrictive
background-check law?

I chose background checks because they are one of the more common law
types in the dataset. Between 2015 and 2023, 12 states adopted
restrictive background-check laws. Since these years overlap with the
firearm death data, I can compare states that adopted these laws with
those that did not.

### Study Design

Each observation will represent one state in one year from 2014 to 2024.
I’ll use the 2005 data for descriptive purposes but leave it out of the
regression.

For the exposure, I’ll mark a state as exposed starting the year its
first restrictive background-check law took effect. If the law was later
replaced or weakened, I’ll update its status. I’ll also test whether
starting exposure the following year changes the results. Laws with
missing or unclear dates will be excluded from the main analysis.

The main outcome is the firearm death rate per 100,000 people, with
homicide rate as a secondary outcome.

I’ll adjust for year, census region, baseline firearm death rate, and
the number of other restrictive gun laws already in place.

### Methods

First, I’ll organize the law data by state and year and merge it with
the death rates. I’ll summarize laws by type and restrictiveness, map
background-check laws across states, and compare firearm death trends
between adopting and non-adopting states.

For the main analysis, I’ll use a difference-in-differences approach
with state and year fixed effects. I’ll also examine trends before and
after adoption to see whether the groups followed similar patterns
before the laws took effect.

## Part 2: Outbreak severity by how the disease spread

### The data

`waterborne` comes from the CDC’s National Outbreak Reporting System
(NORS), accessed through the CDC open data API. It contains 66,713
outbreaks from 1971 to 2023, including about 1,069 labeled as
“Multistate.”

The dataset includes outbreak year, month, state, transmission mode, and
number of illnesses. Many records also report hospitalizations, deaths,
causes, and settings, although some information is missing.
Hospitalization data are available for about 58,000 records and etiology
for around 50,000.

Since the API returns records with different fields, I’ll need to clean
the data, convert dates, and identify missing values before analyzing
it.

### Research Question

Is the way an outbreak spreads associated with its severity, measured by
the proportion of infected people who are hospitalized?

### Study Design

Each observation will represent one outbreak. I’ll include outbreaks
with a known transmission mode and hospitalization count.

The main exposure is transmission mode: person-to-person, foodborne,
waterborne, or other. The outcome is the proportion hospitalized out of
all reported illnesses. I’ll use hospitalization counts in the model
rather than just the calculated percentages.

I’ll adjust for decade, outbreak setting, cause (norovirus, Salmonella,
Shiga toxin-producing E. coli, or other/unknown), and census region.
Multistate outbreaks will be treated as a separate geographic group.

I’ll also check whether outbreaks with missing hospitalization data
differ from those with complete records.

### Methods

I’ll start by summarizing outbreaks, illnesses, hospitalizations, and
deaths by transmission mode, decade, and region. I’ll compare observed
and expected outbreak counts to identify patterns across regions and
transmission modes.

For the main analysis, I’ll use binomial or quasi-binomial logistic
regression to examine the association between transmission mode and
hospitalization proportion while adjusting for the other variables. If
possible, I’ll also create an interactive map using `leaflet` and
`state.center`.

## Limitations I expect

Both analyses use observational data, so the results will show
associations rather than prove causation.

For Part 1, only 12 states adopted restrictive background-check laws
during the study period, which may limit statistical power. States may
also differ in other ways, and some may adopt several gun laws around
the same time. The gap between 2005 and 2014 also limits the historical
comparisons.

For Part 2, the dataset only includes reported outbreaks, and reporting
practices have changed over time. Missing hospitalization and cause
information could introduce bias. Outbreak severity also depends on the
people affected, which may not be fully captured by the available
variables.
