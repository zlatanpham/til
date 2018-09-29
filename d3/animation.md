# Animation

Animate bar chars with negative values

```javascript
this.svg
        .append('g')
        .selectAll('g')
        .data(data)
        .enter()
        .append('rect')
        .attr('class', 'bar')
        .attr('x', function(d) {
          return d;
        })
        .attr('y', function(d) {
          return d.value > 0 ? height - y(0) : y(0);
        })
        .attr('width', columnWidth)
        .attr('height', 0)
        .transition()
        .duration(1000)
        .delay(function(d, i) {
          return i * 100;
        })
        .attr('y', function(d) {
          return d.value > 0 ? y(d.value) : y(0);
        })
        .attr('height', function(d) {
          return Math.abs(y(d.value) - y(0));
        })
```

