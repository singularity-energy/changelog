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
