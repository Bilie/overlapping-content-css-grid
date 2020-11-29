# Modern layouts with CSS Grid

Let's create a hero component with CSS grid.
Our end result will be a hero UI component, that can features a large image, with some text below and an overlapping image. There will be two varations - one with the large image spanning edge to edge and one with the large image with confined width.

![final hero](hero-final.png)

## Creating Grid

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

### Grid option 1

Our `.content-container` is a now grid that has 3 columns - the most left and most right columns will be empty most of the time, they are the ones creating the extra spacing around our content on larger screens. The content itself will live in the middle column.

```css
.content-container {
    display: grid;
    grid-template-columns: 1fr minmax(auto, 960px) 1fr;
    padding: 0 20px;
}
```

![example content container with grid](example-content-container.png)

To make it even more flexible let's exchange the hardcoded value for the content max width with a CSS custom property:

```css
:root {
    --content-width: 960px;
}

.content-container {
    display: grid;
    grid-template-columns: 1fr minmax(auto, var(--content-width)) 1fr;
    padding: 0 20px;
}
```

That way we have only one place to change when we need to update it - great!

We can set a general rule to center the content:

```css
.content-container > * {
    grid-column: 2 / span 1;
}
```

This way the direct children of the content container will be centered by default.

Whenever we want our content to span edge to edge, we can add a special class to it:

```css
.fullscreen {
    grid-column: 1 / -1;
}
```

So far so good - we can add content with fixed width, and we can add content that stretches full screen.

### Grid option 2

Another approach we can take, is to create 14 columns, first and last will be 1fr centering the centent on screen.
And inbetween we will have 12 columns with fixed width in a absolute unit and fixed gap, also in asbolute unit like `px`.

![example content container with grid with 14 columns](example-content-container-14-columns.png)

```css
.grid {
    grid-template-columns: 1fr repeat(12, 75px) 1fr;
}
```

This approach might be confusing at first, because we would need to count the column slightl different than what is in the design file.

## Creating the hero content

### Option 1

The 3 column grid container will have 2 children - one to hold the full screen image, and one to hold the text content and phone. This 3 column grid has no defined rows.
The rows will be automatically created as we add more content (implicit grid).


If we do not explicitly say on which row the two children should appear, they will be placed where there is space. Since the image is spanning from edge to edge, the next child will take the next available row.


We dont want the two to appear one below each other, but on top of each other.

To achieve this - we specify `grid-row: 1;` to both children.

We want the content to appear right below the image and only the phone to be overlapping.

We turn the hero text content container into a grid with 12 columns and 3 rows. The last row will be set to auto, so it can grow as the content grows.

The first and second row should sum up to the height of the full screen image.

So if we want our full screen image to be 400px hight, then the 1st two rows will be 200px each - if we want two rows with equal height.

Again, to improve maintanability we can make use of CSS variables.

```css
grid-template-rows: var(--hero-image-height / 2) calc(var(--hero-image-height) / 2) auto;
```

We might want a different amount of overlapping, and not exactly half the image height.
To do this, let's add one more variable, which will be the height of the phone.
The overlap amount, which is the size of the second row, will be a percentage of the height of the phone.

```css
--overlap: calc(var(--phone-height) * 0.8);
```

This way we have one place to change when adjusting the overlap size. With value of `0.8`, 80% of the phone will be overlapping the full width image. With a value of a `0.2`, only 20% will be overlapping the full width image.

Now the final value for the grid rows will be:

```css
grid-template-rows: calc(var(--hero-image-height) - var(--overlap)) var(--overlap) auto;
```

The first row is the total height of the image minus the overlap percentage. The second row has the size of the overlap.
And the last row remains as auto, so it can grow in height as the content grows.

When using css properties - we only need to change the value of the custom property, and the grid definition will be updated accordingly.

### Option 2

With a grid with 14 columns we can create the overlap a bit easier.

the hero component will have 3 rows, like we had before

We have the image spanning all 14 columns, and taking up the 1st two rows

the hero content will span 12 columns with an offset of 1

the text content will span on 7 columns, and take up the last row

the phone will span 2 rows, and take the remaining free columns

## Conclusion

We can use grid to create container that centers content on screen

We can use 2 overlapping grids, to create the effect of overlapping content

We can use 1 grid, but 14 columns might be difficult to think about