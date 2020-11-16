# Modern layouts with CSS Grid

Let's create a hero component with CSS grid.

## Hero with fullwidth image

First things first - we will need to create a container that holds the content, centers it on screen and gives some padding on smaller screens.

A commond CSS recipe to do this would be:

```css
.content-container {
    max-width: 900px;
    margin: 0 auto;
    padding: 0 20px;
}
```

This way we have a very simple but fully responsive content container.

Let's refactor this recipe to use CSS Grid instead.

```css
.content-container {
    display: grid;
    grid-template-columns: 1fr minmax(auto, 900px) 1fr;
    padding: 0 20px;
}
```

To make it even more flexible let's exchange the hardcoded value for the max width of the contnet with a CSS variable:

```css
:root {
    --content-width: 900px;
}

.content-container {
    display: grid;
    grid-template-columns: 1fr minmax(auto, var(--content-width)) 1fr;
    padding: 0 20px;
}
```

That way we have only one place to change when we need to update it - great!

Our content-container is a grid that has 3 columns - the most left and most right columns will be empty most of the time, they are the ones creating the extra spacing around our content on larger screens. The content itself will live in the middle column.

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

Let's create the container that will hold the hero component.

It will have class `.content-container` and it will have 2 children - one to hold the full screen image, and one to hold the text content and phone.

When we created the `.content-container` we did not specify any rows. The rows will be automatically created as we add more content (implicit grid).
If we do not explicitly say on which row the two children of `.content-container` should appear, they will be placed where there is space. Since the image is spanning from edge to edge, the next child will take the next row.

We dont want the two to appear one below each other, but on top of each other.

To achieve this - we specify `grid-row: 1;` to both children.

We want the content to appear right below the image and only the phone to be overlapping.

We turn the hero text content container into a grid with 3 rows. The last row will be set to auto, so it can grow as the content grows.

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

**TODO: use final height for var(--hero-image-height)**

we can have 2 grids that are overlapping each other

we create one grid first that will be our content container

1fr minmax(auto, 900px) 1fr

we position the full width image from 1 to -1 - taking up all the space
the image container should also have a fixed height

we can make use of object fit, to position the image fit in the container

the content iteself will take only the middle column

we will create a grid in the hero text container as well
we will add 12 columns, with the column gap - 20px
rows - the last row will be auto - it should grow as the content grows
the 1st two rows should be the height of the image divided by 2
the 2nd row we can change - depending on how much we want the phone to overlap over the image
image heigt divided by 2, then multiplied by % to be overlapping

first row remains empty
the hero text will take the last row, and the first 6 columns
the phone will take the 2nd and 3rd row, but be aligned to the top

## Card Hero

- we get loading effect for free

-> note - how to make full screen images with grid
-> note - create your own design layout grid with CSS grid