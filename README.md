# Modern layouts with CSS Grid: Overlapping content

Let's create a hero component with CSS grid.

Our end result will be a hero UI component, that can feature a large image, with some text below and an overlapping image. 

![final hero](hero-fullwidth-final.png)

## Creating content container

First things first - we will need to create a container that holds the content, centers it on screen and gives some padding on smaller screens.

A common CSS recipe to do this would be:

```css
.content-container {
    max-width: 900px;
    margin: 0 auto;
    padding: 0 20px;
}
```

This way we have a very simple but fully responsive content container.

Let's refactor this recipe to use CSS Grid instead.

## Content container wih CSS Grid: 3 column grid

First, we will define a grid container that has 3 columns:

```css
    
    grid-template-columns: minmax(20px, 1fr) minmax(min-content, var(--content-width)) minmax(20px, 1fr);
```

The most left and the most right column will be empty most of the time. 
They are the ones creating the extra spacing around our content on larger screens. We will use these 2 columns when we need the content to span edge to edge. The main content will live in the middle column.

The first and last column will be at least 20px wide, this will give us space on mobile.

The middle column will be as wide as the content, but not expand beyond the width we have set, in this case 1120px.

![example content container with grid](example-content-container.png)

Let's add the variable responsible for the size of the content:

```css
:root {
    --content-width: 1120px;
}
```

That way we have only one place to change when we need to update it - great!

We can also set a default rule to center any content that is direct child of the 3 column grid:

```css
.grid > * {
    grid-column: 2 / span 1;
}
```

Whenever we want our content to span edge to edge, we can add a special class to the HTML element:

```css
.fullscreen {
    grid-column: 1 / -1;
}
```

So far so good - we can add content with fixed width, and we can add content that stretches full screen.


## Creating the hero content

The 3 column grid container will have 2 children. One will hold the full screen image, and the other one will hold the hero text content and phone. 

So far the 3 column grid has no defined rows and so rows will be automatically created as we add more content (implicit grid).

If we do not explicitly say on which row the two children should appear, they will be placed where there is space. Since the image is spanning from edge to edge, the next child will take the next available row.

We don't want the two to appear one below each other, but on top of each other a little.

With CSS Grid we ca achieve this easily, by placing the two items in the same row: we specify `grid-row: 1;` to both children.

So we are getting closer, but not quite. We want the content to appear right below the image and only the phone to be overlapping the hero image. We also want the text and phone to appear side by side.

We will transform the hero text content container into a grid with 12 columns and 3 rows. 

Each column will be 1 fraction of the free space, and we can add a gap of `20px`. This way we can position the text and phone right next to each other.

What we want to achieve with the rows is to push the text content down, so we can see the hero image through. The first and second row should sum up to the height of the full screen image. The last row will be set to auto, so it can grow as the content grows.

For example, if we have the hero full screen image set to 400px height, then the 1st two rows should sum to the same number, we can assign each of them to be 200px - if we are okay with two rows with equal height.

Again, to improve maintanability we can make use of CSS variables.

```css
/* General 12 column content grid, we can reuse this class with other components */
.content-grid {
    display: grid;
    grid-gap: var(--column-gap);
    grid-template-columns: repeat(12, 1fr);
}

.hero-content {
    grid-template-rows: var(--hero-image-height / 2) calc(var(--hero-image-height) / 2) auto;
}

```

We might want a different amount of overlapping, and not exactly half the hero image height.

To calculate how much overlap we want, we need to know the size of the phone, so let's add this as variable.

The overlap amount, which is the size of the second row, will be a percentage of the height of the phone.
We can save the percentage in a variable, so we can adjust it on different screen sizes as needed:

```css
:root {
    --overlap-percentage: 0.6;
}
```

This way we have one place to change when adjusting the overlap size. With value of `0.8`, 80% of the phone will be overlapping the full width image. With a value of a `0.2`, only 20% will be overlapping the full width image.

Now the final value for the grid rows will be:

```css
.hero-content {
    /* Calculate overlap size */
    --overlap: calc(var(--phone-height) * var(--overlap-percentage));

    grid-template-rows: calc(var(--hero-image-height) - var(--overlap)) var(--overlap) auto;
}
```

The first row is the total height of the image minus the overlap percentage. The second row has the size of the overlap. The last row remains as `auto`, so it can grow in height as the content grows.

![example full width hero with grid](hero-fullwidth-content-grid.png)

Since we are using CSS custom properties, in our media queries we only need to change the value of the custom property, and the grid definition will be updated accordingly. For example, this is how we can adjust the overlap amount on larger screens:

```css
@media (min-width: 700px) {
    :root {
        --overlap-percentage: 0.3;
    }
}
```

## Content container wih CSS Grid: 14 columns grid 

Another approach we can take, instead of using 2 overlapping grid containers, we can use one grid container.
This grid container will have 14 columns. The first and last columns will be 1fr centering the centent on screen.

Inbetween we will have 12 columns with fixed width in a absolute unit and fixed gap, also in absolute unit, `px`.
For larger screens, the sum of these columns and column gaps should amount to the maximum size of our content:

```css
:root {
    --column-gap: 20px;
}

.content-grid {
    grid-gap: var(--column-gap);
    grid-template-columns: 1fr repeat(12, 75px) 1fr;
}
```

![example content container with grid with 14 columns](example-content-container-14-columns.png)

This approach might be confusing at first: we would need to count the column slightly different than what is in the design file. All content will be have an offset of one column, unless we want the content to stretch edge to edge.

On smaller screens, we need to hide the first and last columns, and use the column gap to give us space around the content:

```css
.content-grid {
    grid-template-columns: 0 repeat(12, 1fr) 0;
}
```

It is important that we exchange the absolute units for flexible units like `fr`, so to avoid our grid overflowing.

## Creating the hero content

With a 14 columns grid we can create the overlap a bit easier.

The hero component will have again 3 rows, just like before. The logic remains the same - the first two rows should sum to the total height of the hero image. The hero image will be spanning over all 14 columns, and taking up the first two rows.

The hero text and phone will no longer be wrapped in one container. Instead, the hero text will span over 7 columns, with an offset of 1 column. It will again take up the last row. The phone will span over 2 rows, and take the remaining free columns, but the very last one.

![Hero fullwidth with one 14 columns grid](hero-fullwidth-14-columns.png)

## Creating the hero content: variations

We can easily create variations of this layout. For example we can create a hero card component, where the image does not stretch from edge to edge but is as wide as the content.

![final hero](hero-card-final.png)

## Conclusion

We saw how we can utilize CSS Grid to create a nice layout with overlapping elements.
We also transformed a common CSS recipe that centers content on screen to use CSS grid.
We could use 2 overlapping grids or we can use 1 grid.
The 14 columns approach seemed easier with fewer extra elements, but it might be more difficult to think about it.

For this walk through, I have been using the CSS grid inspector in Firefox.
The images are fro Unsplash.
