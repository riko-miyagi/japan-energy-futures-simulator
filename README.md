# Japan Energy Futures Simulator

**[Live demo →](https://riko-miyagi.github.io/japan-energy-futures-simulator/)**

An interactive scenario tool projecting Japan's electricity generation mix and grid carbon intensity through 2040. Move three sliders — how much nuclear power comes back online, how fast renewables grow, how fast coal gets phased out — and watch the projection recalculate live, built on Japan's real 2000-2024 history and standard energy-sector emission factors.

## Why this exists

Japan's electricity system went through a dramatic, forced transition after the March 2011 Fukushima accident: nuclear power, which supplied about a quarter of the country's electricity, was shut down almost overnight. Natural gas filled most of the gap. Renewables have grown steadily since, but slowly. This tool lets you explore what different policy paths from here would actually do to Japan's grid — not as a precise forecast, but as a way to build intuition for how the pieces trade off against each other.

## What it does

- Renders Japan's actual 2000-2024 electricity generation mix as a stacked area chart (7 fuel categories: nuclear, coal, natural gas, oil, other fossil gases, hydroelectric, non-hydro renewables).
- Lets you set a target year (2025-2040) and three policy levers: nuclear restart pace (as a % of the 2010 pre-Fukushima peak), renewable annual growth rate, and coal annual phase-down rate.
- Projects the mix forward from 2024 under your chosen scenario, with natural gas absorbing whatever's left to meet total demand — the same role it actually played after 2011.
- Calculates projected grid carbon intensity (gCO2/kWh) for your target year and compares it to the 2024 baseline.
- Includes four one-click presets, including Japan's actual stated 2030 government energy policy target.
- Tracks **energy import dependency** for your scenario — the % of generation coming from imported fuel sources (coal, LNG, oil), since Japan imports nearly all its fossil fuel. Nuclear is classified as quasi-domestic, matching Japan's own METI convention.
- **Shareable scenarios**: your slider settings are encoded into the page URL automatically, so copying the link and sending it to someone reopens your exact scenario.
- **CSV export**: download the full historical + projected dataset for your current scenario, including generation by fuel type, carbon intensity, and import dependency per year.

## How the model works

Starting from 2024's actual generation mix:

- **Nuclear** moves linearly from its 2024 level toward your chosen target (a % of 2010's pre-Fukushima peak of 278 TWh).
- **Coal** declines at your chosen compound annual rate.
- **Non-hydro renewables** (mostly solar) grow at your chosen compound annual rate.
- **Oil**, **other fossil gases**, and **hydroelectric** are held at 2024 levels — all three are small and structurally fairly fixed in Japan's system.
- **Natural gas** absorbs whatever is left to meet total demand, which is held flat at the 2024 level.
- If a scenario's nuclear + coal + renewables + the fixed categories would exceed total demand, renewable growth is capped so the model never implies negative gas generation — this only triggers under fairly extreme slider combinations.

Carbon intensity is calculated using standard lifecycle emission factors (gCO2eq/kWh) per fuel type:

| Fuel | Factor (gCO2eq/kWh) |
|---|---|
| Nuclear | 12.5 |
| Hydroelectric | 24.9 |
| Non-hydro Renewables | 98.7 |
| Natural Gas | 467.6 |
| Other Fossil Gases | 675.4 |
| Oil | 727.4 |
| Coal | 935.2 |

These start from literature-standard (IPCC-style) median lifecycle emission factors, with the non-hydro renewables figure blended to reflect Japan's actual solar/biomass/wind/geothermal split within that category (dominated by solar, with a meaningful biomass contribution). A single uniform scaling correction (+3.9%) was then fitted to account for accounting-scope differences between the two source datasets.

## Validation

The model was checked against Japan's actual historical carbon intensity, 2000-2024:

- **R² = 0.978**
- **Mean absolute error: 1.4%**

This is a simplified scenario-exploration tool, not a precision forecast. It assumes flat electricity demand and holds three minor fuel categories constant. Treat projected numbers as directionally meaningful, not as an exact prediction — the point is to build intuition for the trade-offs, not to replace a real energy system model.

## Preset scenarios

| Preset | Target year | Nuclear restart | Renewable growth | Coal phase-down |
|---|---|---|---|---|
| Business as Usual | 2030 | 25% of 2010 peak | 8%/yr | 2%/yr |
| Government Target (2030) | 2030 | 69% of 2010 peak | 9%/yr | 3%/yr |
| Aggressive Renewables, No Nuclear | 2035 | 0% | 15%/yr | 6%/yr |
| Nuclear Restart Heavy | 2030 | 90% of 2010 peak | 5%/yr | 4%/yr |

The "Government Target" preset approximates Japan's 6th Strategic Energy Plan (2021), which targets roughly 20-22% nuclear and 36-38% renewables in the power mix by FY2030.

## Why import dependency matters here

Japan imports almost all of its fossil fuel — coal, LNG, and oil are sourced almost entirely from overseas, a fact that has shaped Japanese energy policy since the 1970s oil shocks and again after the post-Fukushima surge in LNG imports. Energy security is arguably as central to Japanese energy policy debates as carbon emissions are, so this tool tracks both rather than treating carbon as the only outcome that matters.

## Data sources

- **Fuel mix (2000-2024):** U.S. Energy Information Administration, International Energy Statistics — Japan, Electricity Generation. See [`data/japan_energy_mix_2000_2024.csv`](data/japan_energy_mix_2000_2024.csv).
- **Historical carbon intensity, used for model validation:** Ember, Yearly Electricity Data (Global).

## Tech stack

Plain HTML, CSS, and JavaScript — no build step, no framework. Charting via [Chart.js](https://www.chartjs.org/) (loaded from CDN). This is a standalone project with its own visual identity, separate from [riko-miyagi.github.io](https://riko-miyagi.github.io), which links to it.

## Running it locally

No build process — clone the repo and open `index.html` directly in a browser, or serve the folder with any static file server.

```
git clone https://github.com/riko-miyagi/japan-energy-futures-simulator.git
cd japan-energy-futures-simulator
open index.html
```

## License

MIT — see [LICENSE](LICENSE).
