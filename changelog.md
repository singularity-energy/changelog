<sub>May 12, 2022</sub>
### New Documentation

As promised in our last changelog, we've been hard at work on our new documentation center. Take a look: https://singularity-energy.stoplight.io/. In addition to interactive API endpoint documentation, we now have articles explaining our geographic coverage, emissions methodology, carbon event terminology, and more.

We think this is a big improvement over our current docs, and should help users get the most out of our API. If you have any feedback, suggestions, comments, feel free to email us at info@singularity.energy.

#### Security Improvements

Security is a continuous process, and we're always making improvements and monitoring our systems in the background. No news is good news here - hopefully you'll never have to think about our security practices! However, there's one new development to highlight: we've initiated a third party penetration test, to ensure our systems and controls are solid. This will be an annual exercise, and once the first test is complete we'll be happy to provide the resulting report upon request.


<br />
<hr />

<sub>Apr 12, 2022</sub>
### Compliance API

![Screenshot of embedded compliance widget](https://raw.githubusercontent.com/singularity-energy/changelog/main/compliance.png)

We've released a new [Compliance API](https://singularity-energy.stoplight.io/docs/compliance-api/q9jvlplq-singularity-compliance-api), along with the companion web app shown above, to allow building owners to explore how they're performing against applicable carbon regulations, and what their options are to reduce emissions. The initial release covers New York City's [Local Law 97](https://www1.nyc.gov/site/sustainablebuildings/ll97/local-law-97.page), and future releases will cover Boston's [BERDO](https://www.boston.gov/departments/environment/building-emissions-reduction-and-disclosure) and beyond, as climate regulations (hopefully) sweep the nation in coming years.

If you're interested in trying out the API or embedding the compliance tool into your own web app, reach out!

#### New API Docs

No more Google Docs! We're testing a new documentation platform - [check it out](https://singularity-energy.stoplight.io/). This is still a work in progress, so you can expect more improvements and content before this goes live in the next few weeks. As always, if you have any feedback, we're all ears.


<br />
<hr />

<sub>Mar 7, 2022</sub>
### Expanding Singularity's Team

We’ve quadrupled the size of our engineering team, bringing on two new software engineers and a data scientist. With more engineering bandwidth, you can expect lots of upcoming improvements to API performance, data quality, and documentation.

One of the first priorities of our expanded team has been to build on the data quality improvements described in past changelogs (for some background, take a look at our October 2021 changelog). Each of the data sources we collect from has a different set of anomalies, time gaps, and other issues that need to be detected and fixed. To ensure consistent data quality,we customize issue detection and handling for each source. This month, we’ve added data issue handling for EIA, and expanded our quality checks for ISONE.


#### Improvements and fixes:
- We updated our handling of EIA data issues to reflect the detection and imputation of anomalous data performed during EIA’s own analysis. This means you have access to the same imputed data EIA uses to compile monthly and annual summaries – but we flag imputed values, so you know whether you’re seeing real or imputed data
- We now audit our database daily to detect missing and anomalous data from EIA and ISONE and re-collect it when possible

<br />
<hr />

<sub>Jan 15, 2022</sub>
### Imports and Exports

The simplest method of calculating grid carbon intensity is to look at the fuel mix reported by the grid operator - a breakdown of which types of power plants are producing electricity for the grid at a given time. This is `generated_rate`, the default for much of our API. But this methodology excludes an often important factor: imports and exports. In addition to its own generation, Grid A may be importing fuel from Grid B, which may itself be importing from Grids C and D, and so on. The picture quickly becomes complex. In order to calculate Grid A's carbon intensity, we'll need to look at the fuel mix and imports and exports of potentially many other grids.

The result of this calculation is `consumed_rate`, available for all EIA regions in our API. But this methodology is vulnerable to a wide array of data quality issues. Instead of relying on a single source, we need data from every grid operator in the grid interchange graph. There can also be conflicting data - what happens when New York and New England both claim to be exporting electricity to each other at the same time? Recently we've been working on cleaning up the data underlying our `consumed_rate` calculations, and filling in as many gaps as possible. It's an ongoing process, but if you're using this rate you'll see more consistent results as we're constantly improving the data pipeline.

#### We're Hiring!
If you know anyone who is passionate about decarbonization, has software or data science experience, and wants to learn more, check out the job descriptions below and please reach out!

[Jobs at Singularity Energy](https://angel.co/company/singularity-energy/jobs)

<br />

***

<sub>Dec 6, 2021</sub>
### Negative Generators

What happens when a power plant is reporting negative power? Thanks to a tip from an API user, we've been looking into this recently: what does it mean when a region says it produced -4 MW from natural gas?

Power plants consume electricity themselves, and occasionally, when they're still running but their output isn't needed for a time, they might burn more power than they generate. Even more occasionally, sometimes demand for a particular fuel type in a region is so low that all power plants of that type are sitting idle, and the total region-wide sum of power generated is negative.

So, what's the carbon intensity of negative power? This edge case raises some interesting philosophical questions (*Am I responsible for emissions that didn't come from producing any power?*), as well as practical ones: *What's the intensity of an idling natural gas plant?*. It isn't zero, and it certainly isn't negative - these plants aren't absorbing CO<sub>2</sub> out of the air while they're sitting idle.

To be frank, we're still thinking about the best way to handle this, and as is so often the case in carbon accounting, there's more than one valid answer. For now, we're treating these negative values as zeros, with a corresponding carbon intensity of 0 lbs/MWh. The EPA eGRID intensity model [already accounts for negative generation](https://www.epa.gov/egrid/egrid-questions-and-answers#egrid10aa), so over a longer period of time, these brief periods of 0 emissions should be balanced out by slightly higher emissions when generation is positive. This approach is straightforward, but it's not ideal, and you can expect future changelogs to note updates as we continue to investigate.

#### Improvements and fixes:
- Fixed brief outage in EIA generation and interchange scrapers
- Correct misspelled fuel types from some historical sources
- Marginal carbon intensity calculation now better handles missing fuel mix events

<br />
<hr />

<sub>Oct 4, 2021</sub>
### Continuously Improving Data Quality
We collect data from a lot of sources, and we've seen many different ways that data can be flawed. Maybe an ISO unexpectedly changes its reporting format, or reports all 0s for an interval, or stops updating at all for an hour or two, or any number of other problems. We've spent the last couple weeks improving our system of monitoring data quality to detect these issues, and just as importantly, correct them as soon as possible.

We're focused on five dimensions of quality:

- **Completeness**: Are there any gaps in the data? How large and how frequent are they?
- **Timeliness**: How quickly does data arrive in our system after it's published?
- **Validity**: Does the data make sense? Are there [missing fuel types](https://carbonara.energy/changelog#eia-ciso-and-eia-pjm-data-anomalies) or anomalous values?
- **Consistency**: Do different sources for the same information agree? Do our forecasts match the real data?
- **Integrity**: Do we have the full chain of events, from (for example) raw fuel mix data all the way to a subregional carbon intensity?

When we do detect an issue, we'll record it, and automatically check back every once in a while to see if the source has updated data. Often, issues are fixed within the hour, or the next day.

In the future, you'll see more of these dimensions reflected on our [status page](https://carbonara.singularity.energy/status). For now, next time you see a suspiciously high carbon intensity or a fuel mix that drops to zero, check back in a little while and see if it isn't fixed.

#### Improvements and fixes:
- Fixed occasional issue with SPP data just after midnight
- Added `updated_at` metadata to track when events were last modified

<br />
<hr />

<sub>Sep 3, 2021</sub>
### EIA.CISO and EIA.PJM data anomalies

Thanks to the help of an eagle-eyed API user, we've corrected two issues with EIA data. First, there is a 10-month period during which CAISO failed to report the "hydro" and "other" fuel types to the EIA:

![Graph of net generation from hydro for CAISO from the EIA, showing a gap from 10/2019-08/2020](https://raw.githubusercontent.com/singularity-energy/changelog/main/caisogap.png)

We've amended EIA.CISO data during this period with "hydro" and "other" values from direct CAISO data.

In addition, EIA.CISO and EIA.PJM data appears to be consistently one hour behind direct data from CAISO and PJM. We believe these two ISOs are reporting correct data through their own APIs and dashboards, but submitting to the EIA with the wrong timestamps, causing the mismatch. We've timeshifted EIA.CISO and EIA.PJM to correct the  issue, and we'll continue working with the EIA to identify any other regions that may be affected by this.

#### Improvements and fixes:
- Forecast lookahead monitors on the [status page](https://carbonara.singularity.energy/status) now show the correct colors
- Removed imports from CAISO generated fuel mix for consistency with other ISOs
- Improved data quality of CAISO and SPP generated fuel mix
