# Environmental Cost Assessment

This document accompanies the GEOL0069 final project (Notebooks 1-3: flood extent mapping and depth
estimation for Fishlake, South Yorkshire). It aims to report the directly measured compute
emissions for this project, and to place that figure in a wider context, in terms of the
additional, unmeasured compute involved in producing this project, and the broader infrastructure
(cloud platforms, satellite data, generative AI tools) that the project made use of.

Throughout, figures are presented as estimates with sources given. The aim is to provide an approximate
order-of-magnitude picture, not a precise audit, and in several places, the
measured figure is a lower bound.

---

## 1. Measured Computation Emissions

Across Notebooks 2 and 3, CodeCarbon recorded a combined total of:

| Metric | Value |
|---|---|
| Energy consumed | 210.83 Wh |
| CO2 emissions | 81.31 g CO2e |

These 67 runs are split across three logged project names:

| Project name | Runs | Notebook | Notes |
|---|---|---|---|
| `Flood_Depth_Regression` | 35 | Notebook 3 | 4 tracker cycles per execution (three GP approaches + SHAP). |
| `Flood_Model_Comparison` | 28 | Notebook 2 | 3 tracker cycles per execution (Random Forest, SVM, CNN). |
| `Flood_RF_classification` | 4 | -- | Originated from an earlier version of Notebook 2 before changes. |

The repeated executions reflect the iterative development of this project, including revisions of 
methodology and code structure, in particular for Notebook 3.

**Note on `Flood_RF_classification`**: These runs were part of a previous iteration of 
Notebook 2. They are included in the totals above for completeness, even though they do not correspond 
to any result reported in this project.

---

## 2. What CodeCarbon's Figure Represents

CodeCarbon estimates emissions as energy consumed multiplied by the carbon intensity of the electricity
grid powering that computation. On Google Colab, the underlying hardware location is not exposed to the
user, so the carbon intensity CodeCarbon applies is not directly verifiable from within the notebook.

Dividing the two totals above gives an implied carbon intensity for this project's compute:

$$\frac{81.31 \text{ g}}{210.83 \text{ Wh}} \times 1000 \approx 386 \text{ g CO2/kWh}$$

For comparison, the UK grid's average carbon intensity in 2025 was approximately **126 g CO2/kWh**
(Carbon Brief's analysis of National Energy System Operator and DESNZ data; Carbon Brief, 2026). The
implied intensity for this project's Colab session is therefore roughly **3.1 times higher** than the
UK grid average.

The most likely explanation is that CodeCarbon, unable to determine the specific region of this Colab session, 
applied either a higher-intensity regional default (e.g. a US grid average, common for Colab backends) or a 
global fallback value. CodeCarbon's documented fallback, used whenever a session's country-level or grid-mix 
data is unavailable, is **475 g CO2eq/kWh**, sourced from the IEA's *Global Energy & CO2 Status Report 2019* 
(IEA, 2019) - and 386 g/kWh sits between the UK figure and that fallback.

**Conclusion**: the 81.31 g CO2e figure should be interpreted as a potential estimate for the energy
actually consumed (210.83 Wh), under an assumed grid intensity that this project cannot verify. This assumed
value is likely higher than the grid that would power the equivalent computation on the author's own device,
if it were to be run locally.

---

## 3. Measured Figure in Context

To aid interpretation, the measured figures are compared below against everyday quantities. These
comparisons are just for illustration, as household appliance power draws and battery capacities vary by
model.

| Quantity | Measured value | Approximate equivalent | Source for comparison |
|---|---|---|---|
| CO2 (as reported, 81.31 g) | 81.31 g CO2e | ~630 m of driving in an average UK car | UK DfT 2024 average car emissions of 129.4 g CO2/km |
| CO2 (if powered by the UK grid, ~26.6 g) | 26.6 g CO2e | ~206 m of driving in an average UK car | as above |
| Energy (210.83 Wh) | 210.83 Wh | ~17.6 full smartphone charges | typical smartphone battery, ~12 Wh |
| Energy (210.83 Wh) | 210.83 Wh | ~21.1 hours running a 10 W LED bulb | typical LED bulb, 10 W |

---

## 4. Carbon Impact Not Captured by CodeCarbon

### 4.1 Untracked notebook executions

CodeCarbon only measures cells where an `EmissionsTracker` is explicitly started and stopped. Notebook 1
(data acquisition via Google Earth Engine) contains no tracker and is therefore entirely untracked.
Beyond this, both Notebook 2 and Notebook 3 were run considerably more than the runs implied by Section
1's tracked-run counts during development, as earlier cells (data loading, feature construction,
visualisation) were re-run many additional times without triggering a tracker. The same applied to cells that produced
errors before reaching a `tracker.start()` call.

### 4.2 Author's laptop use

Development, debugging, and writing of this project involved an estimated **50-60 hours** of laptop use
beyond the Colab sessions tracked above. Using a typical laptop power draw of 25-50 W under active use
(browser, code editor, and Colab tabs open), and the UK 2025 grid intensity of ~126 g CO2/kWh:

| Assumption | Energy (50 h) | Energy (60 h) | CO2e (50 h) | CO2e (60 h) |
|---|---|---|---|---|
| 25 W (low) | 1.25 kWh | 1.50 kWh | ~158 g | ~189 g |
| 40 W (central) | 2.00 kWh | 2.40 kWh | ~252 g | ~302 g |
| 50 W (high) | 2.50 kWh | 3.00 kWh | ~315 g | ~378 g |

Under the central assumption, laptop use over the course of this project (~252-302 g CO2e) is estimated to
exceed the entire CodeCarbon-measured Colab footprint (81.31 g CO2e) by a factor of roughly 3.1-3.7. This illustrates
that even when using tools like CodeCarbon to track emissions, the figure returned is not necessarily the largest 
contributor.

### 4.3 Generative AI assistance

Parts of this project's development, such as debugging error messages and working through implementation
problems, made use of AI assistants, including Google's Gemini integration within the Colab interface.

Google's August 2025 disclosure of Gemini's per-prompt environmental impact gives a median text prompt as
0.24 Wh of energy and 0.03 g CO2e (using Google's market-based emissions accounting, which reflects its
renewable energy purchases) (Google, 2025). For an estimated number of prompts on the order of several
tens over the course of this project, this corresponds to:

| Prompts | Energy | CO2e | Water (at 0.26 mL/prompt) |
|---|---|---|---|
| 20 | ~4.8 Wh | ~0.6 g | ~5.2 mL (~1 teaspoon) |
| 60 | ~14.4 Wh | ~1.8 g | ~15.6 mL (~3 teaspoons) |

These figures are small relative to the totals elsewhere in this document. However,
that Google's figures are likely for short text prompts and reflect a particularly energy-efficient,
production-optimised system; published independent benchmarks for other models and longer or more complex
prompts report energy use ranging from well under 1 Wh up to tens of Wh per prompt depending on model,
reasoning mode, and prompt length, with one widely-cited benchmark reporting a more than 65-fold spread
between the most and least efficient systems on comparable long prompts. This wide spread
reflects ongoing disagreement in the field about how to measure this consistently.

---

## 5. Wider Infrastructure Context

### 5.1 Satellite data sources

This project relies on three data sources from the European Space Agency's Copernicus programme:
Sentinel-1 SAR, Sentinel-2 optical imagery, and the Copernicus DEM (GLO-30). These satellites have
manufacturing, launch, and operational footprints. However, Copernicus data is free, openly licensed, and
used by a vast global community of researchers, businesses, and public bodies, and so the satellites' footprint is
divided across this entire user base and is not meaningfully separable on a per-project or per-download
basis. This project's use of these datasets does not represent an additional or marginal demand on this
infrastructure beyond what already exists.

### 5.2 Cloud computation: Google Earth Engine and Colab

All SAR, optical, and DEM data in this project was processed server-side via Google Earth Engine, and all
model training ran on Google Colab - both hosted in data centres whose aggregate footprint is substantial
and growing. The IEA's 2025 *Energy and AI* report estimates that data centres accounted for approximately
1.5% of global electricity consumption in 2024 (415 TWh), a figure projected to roughly double by 2030 (IEA,
2025). AI-specific workloads are estimated to account for 5-15% of current data centre electricity use,
potentially rising to 35-50% by 2030 (IEA, 2025; Carbon Brief, 2025).

This project's share of that 415 TWh is a very small fraction, and is not estimated numerically here.
Data centre trends are however important context, as this project sits within a computing ecosystem 
whose electricity demand is growing roughly four times faster than overall global electricity demand 
(IEA, 2025), and that trend shapes the environment in which projects like this one are being conducted.

### 5.3 Comparison with traditional flood-depth surveying

Estimating flood depth across an area of this size by traditional means would require either
wading/boat-based depth gauging at many points, or a LiDAR survey flight. Both involve direct fuel
use (vehicles, boats, or aircraft) and labour over an extended period, and are likely significantly
more carbon-intensive than the computational footprint described in this document.

---

## 6. Ecosystem and Resource Considerations

CO2 emissions are the most commonly reported metric for computing's environmental impact, but not the only
one. The following considerations are presented as part of the broader landscape this project's tools sit
within.

**Water use for cooling.** Data centres use water (directly, for cooling, and indirectly, via the
electricity grid) to manage the heat generated by servers and AI accelerators. Google's own disclosure
quantifies this at approximately 0.26 mL per Gemini text prompt (Google, 2025), which is negligible on 
an individual scale, but illustrative of a resource consideration distinct from electricity and CO2, 
particularly relevant in regions facing water stress.

**Geographic concentration and local grid impacts.** While data centres represent a small share of global
electricity demand, their impact is highly concentrated in specific regions. Ireland, for example, is
reported to use around 21% of its national electricity for data centres, a figure projected to reach 32% by
2026 (Carbon Brief, 2025), similar concentration is reported in parts of the United States. This means the
ecosystem effect of cloud computing is not evenly distributed, and it is locally significant in a small
number of places.

**Energy mix and indirect effects.** The IEA reports that renewables (wind, solar, hydro) currently supply
around 27% of global data centre electricity, with the remainder from a mix including a substantial coal
component, particularly in some regions (IEA, 2025). The ecosystem implications of electricity generation
(land use for renewables, air pollution and habitat effects from fossil generation, etc) are therefore part
of any cloud-dependent project, as well as Carbon.

**Hardware lifecycle.** Growing demand for AI computation is a driver of demand for the underlying hardware
(GPUs, TPUs, and supporting infrastructure), which has its own emissions (manufacturing) and a
finite operational lifetime, contributing to electronic waste at end of life. This project's contribution
to this is very small but part of an industry-wide trend.

---

## 7. Other Minor Considerations

The following were considered but are not separately quantified, as their contribution is
expected to be negligible relative to the figures above:

- **Data storage**: the processed arrays, figures, and result tables produced by this project total well
  under 1 GB, stored on Google Drive. Storage represents a small fraction of data centre electricity use
  (estimated around 5%; IEA-derived figures), and this project's share of that is correspondingly small.
- **Network data transfer**: Earth Engine queries return cropped, resampled arrays (on the order of tens of
  thousands of pixels per scene) rather than full satellite scenes; the resulting data transfer is small
  and not separately estimated here.
- **Repository hosting**: the GitHub repository hosting this project's code and documentation represents a
  further negligible, shared infrastructure cost.

---

## 8. Summary

| Component | Estimated CO2e |
|---|---|
| CodeCarbon-measured compute (Notebooks 2-3) | 81.31 g |
| Author's laptop use (50-60 h, central estimate) | ~252-302 g |
| Generative AI assistance (tens of prompts, Gemini-based estimate) | ~0.6-1.8 g |
| **Total estimated** | **~334-385 g** |

Even accounting for laptop use and AI assistance alongside the measured compute figure, this project's
total estimated footprint is on the order of **330-385 g CO2e** - roughly equivalent to **2.6-3.0 km of
driving** in an average UK car (DfT 2024 data). This remains modest in absolute
terms. It is however important to consider that the calculated CodeCarbon value (81.31 g) likely 
represents only around a **fifth to a quarter** of a fuller estimate of the carbon cost of the project's
development(including laptop use).

---

## 9. Sources

- Lacoste, A., Luccioni, A., Schmidt, V., & Dandres, T. (2019). *Quantifying the Carbon Emissions of Machine
  Learning*. arXiv:1910.09700. [arxiv.org/abs/1910.09700](https://arxiv.org/abs/1910.09700)
- International Energy Agency (2019). *Global Energy & CO2 Status Report 2019*. (Source of CodeCarbon's
  475 gCO2eq/kWh world-average fallback intensity.)
  [iea.org/reports/global-energy-co2-status-report-2019/emissions](https://www.iea.org/reports/global-energy-co2-status-report-2019/emissions)
- Schmidt, V., Goyal, K., Joshi, A., Feld, B., Conell, L., Laskaris, N., Blank, D., Wilson, J., Friedler, S.,
  & Luccioni, S. (2021). *CodeCarbon: Estimate and Track Carbon Emissions from Machine Learning Computing*.
  [github.com/mlco2/codecarbon](https://github.com/mlco2/codecarbon)
- Carbon Brief (2026). *Analysis: UK renewables enjoy record year in 2025 -- but gas power still rises*.
  [carbonbrief.org](https://www.carbonbrief.org/analysis-uk-renewables-enjoy-record-year-in-2025-but-gas-power-still-rises/)
  (UK 2025 average grid carbon intensity: 126 g CO2/kWh.)
- Erin Yurday (2026). *Average CO2 Emissions per Car in the UK*, citing UK Department for Transport 2024
  data. [nimblefins.co.uk/average-co2-emissions-car-uk](https://www.nimblefins.co.uk/average-co2-emissions-car-uk)
  (Average car: 129.4 g CO2/km.)
- International Energy Agency (2025). *Energy and AI*.
  [iea.org/reports/energy-and-ai](https://www.iea.org/reports/energy-and-ai) (Data centre electricity
  consumption: ~415 TWh / 1.5% of global electricity in 2024, projected to roughly double by 2030;
  renewables ~27% of data centre electricity.)
- Carbon Brief (2025). *AI: Five charts that put data-centre energy use -- and emissions -- into context*.
  [carbonbrief.org](https://www.carbonbrief.org/ai-five-charts-that-put-data-centre-energy-use-and-emissions-into-context/)
  (Ireland data centre electricity share; AI share of data centre electricity 5-15%, rising to 35-50% by
  2030.)
- Google (2025). *Measuring the environmental impact of AI inference*. Google Cloud Blog, August 2025.
  [cloud.google.com/blog/.../measuring-the-environmental-impact-of-ai-inference](https://cloud.google.com/blog/products/infrastructure/measuring-the-environmental-impact-of-ai-inference/)
  (Median Gemini text prompt: 0.24 Wh, 0.03 g CO2e, 0.26 mL water.)
- Independent 2025 benchmark estimates for per-prompt energy use across large language models, including
  *How Hungry is AI? Benchmarking Energy, Water, and Carbon Footprint of LLM Inference* (arXiv:2505.09598),
  reporting energy use from well under 1 Wh up to tens of Wh per prompt depending on model, reasoning mode,
  and prompt length, with more than a 65-fold spread between the most and least efficient systems benchmarked
  on comparable long prompts.
