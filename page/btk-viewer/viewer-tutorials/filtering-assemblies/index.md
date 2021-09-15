---
title: "Filtering assemblies"
date: "2019-07-31"
---

Assemblies can be filtered interactively by setting variable value ranges or selecting taxonomic categories in the _Filters_ menu. Individual scaffolds or all scaffolds in specific bins can also be filtered based on selections.

Click on the "Filters" tab to open the _Filters_ menu. The examples in this section use log-scaled counts (see [Adjusting plot settings](https://blobtoolkit.genomehubs.org/btk-viewer/viewer-tutorials/adjusting-plot-settings/)) to make the effect of the filters more obvious. To apply a filter based on a variable value, drag one or both of the sliders over the preview histogram:

![](images/Screenshot-2019-07-31-at-09.20.56-1024x831.jpg)

The preview histograms and any plots will update when you release the slider. For example, filtering high and low GC updates the current view:

![](images/Screenshot-2019-07-31-at-09.21.39-1024x831.jpg)

All filter settings are retained when switching to alternate views (see [Exploring views](https://blobtoolkit.genomehubs.org/btk-viewer/viewer-tutorials/exploring-views/)):

- ![](images/ACVV01.cumulative-3-1024x1024.png)
    
- ![](images/ACVV01.snail-2-1024x1024.png)
    
- ![](images/ACVV01.blob_.circle-2-1024x1024.png)
    

Specific values can be set for filters by entering maximum and or minimum values directly, for example setting the minimum length to 5,000:

![](images/Screenshot-2019-07-31-at-09.22.31-1024x831.jpg)

Any filter can be inverted by clicking the _invert_ icon in the field header to exclude rather than include scaffolds matching the filter criteria:

![](images/Screenshot-2019-07-31-at-09.23.12-1024x831.jpg)

To filter based on taxonomic categories, click one or more of the coloured swatches at the top of the preview histogram to toggle the categories on or off. For example, excluding "no-hit" sequences (with the other filters reset to include all scaffolds):

![](images/Screenshot-2019-07-31-at-09.24.37-1024x831.jpg)

To filter based on a selection (see _Using selections_), first make a selection, in this case a purely arbitrary shape:

![](images/Screenshot-2019-07-31-at-09.50.40-1024x831.jpg)

Then click on the _selection_ filter header to activate it:

![](images/Screenshot-2019-07-31-at-09.51.30-1024x831.jpg)

As with the other filters, selection-based filters can be inverted:

![](images/Screenshot-2019-07-31-at-09.51.39-1024x831.jpg)

And combined with other filters, for example, excluding scaffolds with coverage below the low-coverage threshold:

![](images/Screenshot-2019-07-31-at-09.53.28-1024x831.jpg)
