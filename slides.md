---
# try also 'default' to start simple
theme: default
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: true
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
# use UnoCSS
css: unocss
---

# CliMA Soil CO<sub>2</sub> 
Alexandre A. Renchon, Katherine Deck

<div class="abs-br m-6 flex gap-2">
  <button @click="$slidev.nav.openInEditor()" title="Open in Editor" class="text-xl icon-btn opacity-50 !border-none !hover:text-white">
    <carbon:edit />
  </button>
  <a href="https://github.com/slidevjs/slidev" target="_blank" alt="GitHub"
    class="text-xl icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

---

# Presentation layout

* Model presentation
* Model coding (following ClimaLSM framework)
  * Folder & file structure
  * Code snippet
* Model demonstration
* Next steps

---

# Structure

* 2 GitHub branches: CliMAsoilCO2 (PR #114) and soil_co2_params (PR #121)
* 1 GitHub [issue](https://github.com/CliMA/ClimaLSM.jl/issues/113) tracking to do

<br> 

* src/Soil/Biogeochemistry
  * Biogeochemistry.jl  
  * co2_parameterizations.jl 

<br>

* experiment/Soil/Biogeochemistry
  * co2_parameters.jl
  * co2_run.jl

<br>

* test/Soil/Biogeochemistry
  * co2_parameterizations.jl

---

# DETECT model

- PDE: C(z,t) = diffusion + sources
- C(z,t) = CO<sub>2</sub> concentration (mg CO<sub>2</sub> m<sup>-3</sup>) in layer of soil at depth z
- diffusion: vertical transport of CO<sub>2</sub> - f(vertical gradient of C, soil texture, soil moisture)
- sources: microbes and roots - f(T, moisture)

$$
\frac{\delta_{CK}(z,t)}{\delta t} 
= \frac{\delta}{\delta z} (D_{gs}(z,t)\frac{\delta_{CK}(z,t)}{\delta z})
+ S_{K}(z,t)
$$

```julia {all|1-2|4-6} 
@. dY.DETECT.C =
                 -divf2c_C(-interpc2f(p.DETECT.D)*gradc2f_C(Y.DETECT.C))

for src in model.sources
    source!(dY, src, Y, p, model.parameters)
end
```
---

# DETECT model

Figure 1 from DETECT manuscript

<img border="rounded" src="https://gmd.copernicus.org/articles/11/1909/2018/gmd-11-1909-2018-f01.png" style="width:650px;height:400px;">


---
layout: iframe-right

# the web page source
url: https://cupoftea.earth/menu1/ 
# a custom class name to the content
class: my-cool-content-on-the-right

--- 

# Microbe source

- potential: soil organic content, soil microbial pool (z)
- CUE: microbial carbon use efficiency
- pf: fraction of soil organic C that is soluble
- driver: soil temperature, soil moisture
- Low moisture: substrate limitation
- High moisture: oxygen limitation

<br>

# Root source

- potential: soil root biomass (z)
- driver: soil temperature and moisture

---

# CliMA implementation

- ClimaLSM
- ClimaCore
- DifferentialEquations.jl

--- 

# to do list: GitHub issue
- PDE for oxygen (z, t)
- PDE for soil microbial pool, root biomass, soil organic content (z, t)
- CO2 dissolved in water and laterally transported
- CO2 displaced in soil air space by water
- parameterisation (CARDAMOM framework? NEON dataset?) local and global
- mechanisms tweaks: Dani Or, Ruby Fu, Adrian Moure
- CH4 and N2O
- optimality theory

