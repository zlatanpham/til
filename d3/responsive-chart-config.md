---
description: >-
  Sample snippet for bounding a chart inside Viewbox to create responsive
  behavior
---

# Responsive Chart Config

```text
const viewBoxWidth = 1100;
const viewBoxHeight = 300;
const margin = { top: 30, right: 0, bottom: 70, left: 40 };
const width = viewBoxWidth - margin.left - margin.right;
const height = viewBoxHeight - margin.top - margin.bottom;
const columnWidth = 20;
const columnSpacing = 5;
const skills = data.map(t => t.skill);

this.svg = d3
  .select(this.target)
  .append('svg')
  .attr('width', '100%')
  .attr('height', '100%')
  .attr('viewBox', '0 0 ' + viewBoxWidth + ' ' + viewBoxHeight)
  .attr('preserveAspectRatio', 'xMinYMin')
  .append('g')
  .attr('transform', 'translate(' + margin.left + ',' + margin.top + ')');
```

