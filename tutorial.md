# Project: Build a Horizontal Bar Chart with Arcade

Say you have a point feature layer that represents fruit stands in San Francisco. Each point represents the location of a fruit stand, and the attribute tables has counts for various fruits that the stands stock. The stands stock oranges, bananas, kiwis, and apples. You need to make a web map that shows the fruit stands, and you want to quickly show the distribution of fruits at each stand. To do this, you want to make a bar chart. You can do this with Arcade!

[image goes here]

---

# Step 1: Think About the Data

Suppose you manage a layer showing fruit stands. Here is a sample of what the attribute table look like:

| Site Name | Apples | Bananas | Kiwis | Oranges |
| ------- | -------- | ------ | -------- |-------- |
| City Hall Farmer's Market | 15 | 22 | 8 | 30 |
| Ferry Building Farmer's Market | 10 | 2 | 3 | 9 |

Instead of displaying four numbers, we'd like to show one graphic that immediately communicates the distribution.

---

# Step 2: Think about the Function You Would Like to Write

Here is what you know:
* You want your chart to appear in a web map pop up, so you are working in the pop up Arcade profile.
* You have counts of fruits

You need to write a function 

# Step 2: Store the Values in Arrays

Our chart needs three pieces of information:

- The values
- The colors
- The labels

In Arcade, arrays are a convenient way to keep these together.

```javascript
var values = [
    $feature.Apples,
    $feature.Bananas,
    $feature.Kiwis,
    $feature.Oranges
];

var colors = [
    "maroon",
    "gold",
    "MediumSeaGreen",
    "DarkOrange"
];

var labels = [
    "Apples",
    "Bananas",
    "Kiwis",
    "Oranges"
];
```

Notice that the arrays are in the same order.

The first value corresponds to the first color and the first label, the second value corresponds to the second color, and so on.

---

# Step 3: Convert Counts into Percentages

Our fruit counts don't add up to 100%.

Instead, they are raw totals.

The chart needs to know how much of the total each fruit represents.

For example,

```
15
22
8
30
```

becomes

```
20%
29%
11%
40%
```

Rather than doing this calculation every time, we'll create a helper function.

```javascript
function convertArrayToDecimal(array) {
    var decimalArray = [];
    var denominator = Sum(array);

    for (var i in array) {
        decimalArray[i] = array[i] / denominator;
    }

    return decimalArray;
}
```

This function converts raw counts into decimal percentages that add up to **1.0**, making them easier to use when building the chart.

---

# Step 4: Draw One Piece of the Bar

A horizontal bar chart is really just several colored rectangles placed next to each other.

Each rectangle is one HTML table cell.

```html
<td style="background-color:orange; width:40%"></td>
```

Rather than writing HTML by hand every time, we'll create a helper function.

```javascript
function makeBarChunk(color, width) {
    var td = '<td style="background-color:' + color +
             '; width:' + width + '">&nbsp;</td>';
    return td;
}
```

This function accepts:

- A color
- A width

and returns a single section of the finished chart.

---

# Step 5: Build the Legend

A chart isn't very useful without labels.

We'll create a small colored square for each category.

```javascript
function makePatch(color) {
    return '<span style="font-size:medium; color:' +
        color +
        ';">&#9632;</span>';
}
```

This creates a colored square like:

**■ Apples**

using simple HTML.

---

# Step 6: Assemble the Complete Chart

Now we have everything we need.

The `makeBarChart()` function:

1. Converts counts into percentages (optional)
2. Loops through each category
3. Creates each colored section
4. Builds the legend
5. Returns the finished HTML

```javascript
function makeBarChart(colors, values, labels, convert) {

    if (convert) {
        values = convertArrayToDecimal(values);
    }

    var barHtml = '';
    var labelHtml = '';

    barHtml += '<table style="width:100%"><tbody><tr><td><table style="width:100%"><tbody><tr>';
    labelHtml = '<div style="text-align:center"><p style="display:inline-block;text-align:left;">';

    for (var i in colors) {

        if (values[i] > 0.01) {

            var percent = Round(values[i], 2) * 100 + '%';

            barHtml += makeBarChunk(colors[i], percent);

            labelHtml +=
                makePatch(colors[i]) +
                percent +
                ' ' +
                labels[i] +
                ' ';
        }
    }

    barHtml += '</tr></tbody></table></td></tr></tbody></table>';
    labelHtml += '</p></div>';

    return barHtml + labelHtml;
}
```

Notice that very small values (less than 1%) are skipped automatically to keep the chart readable.

---

# Step 7: Add the Chart to a Popup

Once the helper functions are in place, creating a chart only takes a few lines of code.

```javascript
var featureArray = [
    $feature.Apples,
    $feature.Bananas,
    $feature.Kiwis,
    $feature.Oranges
];

var colorArray = [
    "maroon",
    "gold",
    "MediumSeaGreen",
    "DarkOrange"
];

var labelArray = [
    "Apples",
    "Bananas",
    "Kiwis",
    "Oranges"
];

var chart = makeBarChart(
    colorArray,
    featureArray,
    labelArray,
    true
);

return {
    type: "text",
    text: chart
};
```

The resulting popup displays a horizontal stacked bar chart showing the distribution of fruit at each stand.

> **Insert popup screenshot here.**

---

# Step 8: Reuse the Same Code in Dashboards

One advantage of generating HTML is that the exact same chart works in ArcGIS Dashboards.

The chart-building code doesn't change.

Only the return object changes depending on whether you're working in a:

- Dashboard Table
- Dashboard List
- Popup

This means you can build the chart once and reuse it throughout your GIS applications.

> **Insert Dashboard Table screenshot here.**

> **Insert Dashboard List screenshot here.**

---

# Challenge Yourself

Now that you've built one chart, try customizing it.

Ideas:

- Change the colors.
- Add a fifth category.
- Use percentages instead of counts.
- Display inspection status.
- Show election results.
- Visualize utility outages.
- Remove the legend.
- Experiment with different HTML styling.

Each of these modifications helps reinforce the concepts you've learned while producing something useful for your own GIS projects.

---

# Summary

In this tutorial you learned how to:

- Store related data in arrays
- Convert counts into percentages
- Generate HTML using Arcade
- Build reusable helper functions
- Create stacked horizontal bar charts
- Display those charts in ArcGIS Online and ArcGIS Dashboards

Although Arcade doesn't include a charting library, combining Arcade with HTML provides a lightweight and flexible way to visualize data anywhere HTML output is supported.
