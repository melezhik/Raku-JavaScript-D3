# JavaScript::D3 Raku package

This repository has the a Raku package for generation of JavaScript's D3 code for making plots and charts.

This package is intended to be used in Jupyter notebooks with the
[Raku kernel implemented by Brian Duggan](https://github.com/bduggan/raku-jupyter-kernel), [BD1].
The commands of the package generate JavaScript code produce (nice) [D3.js](https://d3js.org/) plots or charts.

See the Jupyter notebook 
["Tests-for-JavaScript-D3"](./resources/Tests-for-JavaScript-D3.ipynb)
for illustrative examples.

The (original versions of the) JavaScript snippets used in this package are taken from 
["The D3.js Graph Gallery"](https://d3-graph-gallery.com/index.html).

--------

## Mission statement

Make first class -- beautiful, tunable, and useful -- plots and charts with Raku using 
concise specifications.

--------

## Design and philosophy

Here is a list of guiding design principles:

- Concise plot and charts specifications.

- Using Mathematica's plot functions for commands signatures inspiration. 
  (Instead of, say, R's ["ggplot2"](https://ggplot2.tidyverse.org).)

  - For example, see [`ListPlot`](https://reference.wolfram.com/language/ref/ListPlot.html), 
    [`BubbleChart`](https://reference.wolfram.com/language/ref/BubbleChart.html).
  
- The primary target data structure to visualize is an array of hashes, 
   with all array elements having the one of these sets of keys 
   - `<x y>` 
   - `<x y group>`
   - `<x y z>`
   - `<x y z group>` 
   
- Multiple-dataset plots are produced via dataset records that have the key "group".  

- Whenever possible deduce the keys from arrays of scalars.

- The data reshaping functions in "Data::Reshapers", [AAp1], should fit nicely into workflows with this package.

- The package functions are tested separately:

  - As Raku functions that produce output for given signatures
  - As JavaScript plots that correspond to the corresponding intents
  
--------

## How does it work?

Here is a diagram that summarizes the evaluation path from a Raku plot spec to a browser diagram:

```mermaid
graph TD
   Raku{{Raku}}
   IRaku{{"Raku<br>Jupyter kernel"}}
   Jupyter{{Jupyter}}
   JS{{JavaScript}}
   RakuInput[/Raku code input/]
   JSOutput[/JavaScript code output/]
   CellEval[Cell evaluation]
   JSResDisplay[JavaScript code result display]
   Jupyter -.-> |1|IRaku -.-> |2|Raku -.-> |3|JSOutput -.-> |4|Jupyter
   Jupyter -.-> |5|JS -.-> |6|JSResDisplay
   RakuInput ---> CellEval ---> Jupyter  ---> JSResDisplay
```

Here is the corresponding narration:

1. Enter Raku plot command in cell that starts with 
   [the magic spec `%% js`](https://github.com/bduggan/raku-jupyter-kernel/issues/100#issuecomment-1349494169).

   - Like `js-d3-list-plot((^12)>>.rand)`.
   
2. Jupyter via the Raku kernel evaluates the Raku plot command.

3. The Raku plot command produces JavaScript code.

4. The Jupyter "lets" the web browser to evaluate the obtained JavaScript code.

   - Instead of web browser, say, Visual Studio Code can be used.

   
The evaluation loop spelled out above is possible because of the magics implementation in the Raku package
[Jupyter::Kernel](https://github.com/bduggan/raku-jupyter-kernel#features), 
[BD1].
   
--------

## Alternatives

### Raku packages

The Raku packages "Text::Plot", [AAp2], and "SVG::Plot", [MLp1],
provide similar functionalities and both can be used in Jupyter notebooks. 
(Well, "Text::Plot" can be used anywhere.)

### Different backend

Instead of using [D3.js](https://d3js.org) as a "backend" it is possible -- and instructive --
to implement Raku plotting functions that generate JavaScript code for the library 
[Chart.js](https://www.chartjs.org).

D3.js is lower level than Chart.js, hence in principle Chart.js is closer to the mission of this Raku package.
I.e. at first I considered having Raku plotting implementations with Chart.js
(in a package called "JavaScript::Chart".)
But I had hard time making Chart.js plots work consistently within Jupyter.

--------

## TODO

In the lists below the highest priority items are placed first.

### Plots

#### Single dataset

1. [X] DONE List plot
3. [X] DONE List line plot
5. [ ] TODO Date list plot
7. [ ] TODO Box plot

#### Multiple dataset

1. [ ] TODO List plot
2. [X] TODO List line plot
3. [ ] TODO Date list plot 
4. [ ] TODO Box plot 

### Charts

#### Single dataset

1. [X] DONE Bar chart
2. [X] DONE Histogram 
3. [X] DONE Bubble chart
4. [ ] TODO Density 2D chart -- rectangular bins
5. [ ] TODO Radar chart 
6. [ ] TODO Density 2D chart -- hexagonal bins
7. [ ] TODO Pie chart

#### Multiple dataset

1. [ ] TODO Bar chart
2. [ ] TODO Histogram
3. [X] DONE Bubble chart
4. [X] DONE Bubble chart with tooltips
5. [ ] TODO Pie chart 
7. [ ] TODO Radar chart

### Decorations

User specified or automatic:

1. [X] DONE Plot label / title
2. [X] DONE Axes labels
3. [X] DONE Plot margins
4. [X] DONE Plot legends (automatic for multi-datasets plots and chart)
5. [ ] TODO Title style (font size, color, face)
6. [ ] TODO Axes labels style (font size, color, face)

### Infrastructural

1. [ ] TODO Support for different JavaScript wrapper styles
  
   - Jupyter cell execution ready
   
   - Standard HTML
   
   - Result output within a JSON format

2. [ ] Better, comprehensive type checking
   
   - Using the type system of "Data::Reshapers" would be ideal, 
     but I do not want to introduce such a "heavy" dependency.
   
3. [ ] JavaScript code snippets management

   - If they become too many

--------

## Implementation details

Initially  the commands of this package were executed in
[Jupyter notebook with Raku kernel](https://raku.land/cpan:BDUGGAN/Jupyter::Kernel)
properly
[hacked to redirect Raku code to JavaScript backend](https://github.com/bduggan/p6-jupyter-kernel/issues/100)

Brian Duggan fairly quickly implemented the suggested Jupyter kernel magics, so, now no hacking is needed.

--------

## References

### Articles

[OV1] Olivia Vane, 
["D3 JavaScript visualisation in a Python Jupyter notebook"](https://livingwithmachines.ac.uk/d3-javascript-visualisation-in-a-python-jupyter-notebook), 
(2020), 
[livingwithmachines.ac.uk](https://livingwithmachines.ac.uk).

[SF1] Stefaan Lippens, 
[Custom D3.js Visualization in a Jupyter Notebook](https://www.stefaanlippens.net/jupyter-custom-d3-visualization.html), 
(2018), 
[stefaanlippens.net](https://www.stefaanlippens.net).

### Packages

[AAp1] Anton Antonov,
[Data::Reshapers Raku package](https://raku.land/zef:antononcube/Data::Reshapers),
(2021-2022),
[GitHub/antononcube](https://github.com/antononcube/Raku-Data-Reshapers).

[AAp2] Anton Antonov,
[Text::Plot Raku package](https://raku.land/zef:antononcube/Text::Plot),
(2022),
[GitHub/antononcube](https://github.com/antononcube/Raku-Text-Plot).

[BD1] Brian Duggan,
[Jupyter::Kernel Raku package](https://raku.land/cpan:BDUGGAN/Jupyter::Kernel),
(2017-2022),
[GitHub/bduggan](https://github.com/bduggan/raku-jupyter-kernel).

[MLp1] Moritz Lenz,
[SVG::Plot Raku package](https://github.com/moritz/svg-plot)
(2009-2018),
[GitHub/moritz](https://github.com/moritz/svg-plot).
