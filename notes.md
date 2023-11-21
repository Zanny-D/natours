# How CSS Works: A Look Behind the Scenes

## The Three Pillars to Write Good HTML and CSS

- Responsive design
- Maintainable and scalable code
- Web performance

### Responsive Design

- Fluid layouts
- Media queries
- Responsive images
- Correct units
- Desktop-first vs Mobile-first

### Maintainable and Scalable Code

- Clean
- Easy to understand
- Growable
- Reusable
- How to organize files
- How to name classes
- How to structure HTML

### Web Performance

- Faster
- Smaller -> So user doesn't have to download as much
- Fewer HTTP requests
- Less code
- Compress code
- Use a CSS preprocessor
- Fewer images
- Compress images

## How CSS Works Behind the Scenes

### What happens when we load up a webpage?

1. Load HTML
2. Parse HTML --> Generates Document Object Model (DOM)
3. Find stylesheets
4. Load CSS
5. Parse CSS
   - Resolve conflicing CSS declarations (cascade)
   - Process final CSS values
6. CSS Object Model (CSSOM) is generated as the result of parsing
7. The DOM and the CSSOM are combined to generate the Render Tree
8. The Render tree is referenced for Website rendering: the visual formatting model
9. Final rendered website is presented

### Terminology

- Selector
- Declaration block
- Property
- Declared value
- Declaration

### The Cascade

Process of combining different stylesheets and resolving conflicts between different CSS rules and declarations, when more than on rule applies to a certain element.

- Author declarations
- User declarations
- Browser declarations (e.g. the blue underlined text)

To determine which take precedence
IMPORTANCE > SPECIFICITY > SOURCE ORDER

1. user !important declarations
2. author !important declarations
3. author declarations
4. user declarations
5. default browser declarations

If we have the same **importance**, we go to **specificity**:

1. inline styles
2. IDs
3. Classes, pseudo-classes, attributes
4. Elements, pseudo-elements

To compute specificity of a selector we determine (Inline, IDs, Classes, Elements)

```CSS
.button {  /* <--------------------------------- (0, 0, 1, 0) we just have the .button class */
  font-size: 20px;
  color: white;
  background-color: blue;
}

nav#nav div.pull-right .button { /* <----------- (0,1,2,2) we have the nav ID, 2 classes (.pull-right and .button), and 2 elements (nav and div) */
  background-color: green;
}

a { /* <---------------------------------------- (0,0,0,1) Just the a element */
  background-color: purple;
}

#nav a.button:hover { /*<---------------------- (0,1,2,1) We have the nav ID,*/
  background-color: yellow;
}
```

- If there's still a tie, then we go to **source order** and the last declaration written in the code will be rendered.
- Only use `!important` as a last resort. It's much better to use correct specificities - **more maintainable code!**
- Inline styles will always have the priority over styles in external stylesheets.
- A selector with 1 ID is more specific than one with 1000 classes
- A selector with 1 class is more specific than one with 1000 elements
- The universal selector "\*" has no specificity value (0,0,0,0), which is why everything takes precedence over it.
- Rely more on specificity than on the order of selectors;
- But, rely on order when using 3rd-party stylesheets -- always put your author stylesheet last.

### How CSS Values are Processed

```HTML
<div class="section">
  <p class="amazing">CSS is absolutely amazing</p>
</div>
```

```CSS
.section {
  font-size: 1.5rem;
  width: 280px;
  background-color: orangered;
}

p {
  width: 140px;
  background-color: green;
}

.amazing {
  width: 66%;
}
```

|                                                                | width (paragraph) | padding (paragraph) | font-size (root)       | font-size (section) | font-size (paragraph) |
| -------------------------------------------------------------- | ----------------- | ------------------- | ---------------------- | ------------------- | --------------------- |
| **1. Declared value** (author declaration)                     | 140px and 66%     | -                   | -                      | 1.5rem              | -                     |
| **2. Cascaded value** (after the cascade)                      | 66%               | -                   | 16px (browser default) | 1.5rem              | -                     |
| **3. Specified value** (defaulting, if there is no cascaded)   | 66%               | 0px (initial value) | 16px                   | 1.5rem              | 24px                  |
| **4. Computed value** (converting relative values to absolute) | 66%               | 0px                 | 16px                   | 24px (1.5 \* 16px)  | 24px                  |
| **5. Used value** (final calculations based on layout)         | 184.8px           | 0px                 | 16px                   | 24px                | 24px                  |
| **6. Actual value** (browser and device restrictions)          | 185px             | 0px                 | 16px                   | 24px                | 24px                  |

### How units are converted from relative to absolute (px)

(see slides)

#### Font-based

em (fonts/lengths)- uses the parent or the current element as a reference
rem - use the root as the reference

#### Viewport-based

vh - 1vh is 1% of the viewport height
vw - 1vh is 1% of the viewport width

#### Recap

- Each CSS property has an initial value which is used if nothing is declared (and there is no inheritance - see next lecture);
- Browsers specify a **root font-size** for each page (usually 16px);
- Percentages and relative values are always converted to pixels;
- Percentages are measured relative to their parent's **font-size**, if used to specify font-size;
- Percentages are meured relative to their parent's **width**, if used to specify lengths;
- `em` are measured relative to their **parent** font-size, if used to specify font-size;
- `em` are measured relative to the **current** font-size, if used to specify lengths;
- `rem` are always measured relative to the **document's root** font-size;
- `vh` and `vw` are simply percentage meaurements of the viewport's height and width.

## Inheritance in CSS

- Inheritance passes the values for some specific properties from parents to children -- **more maintainable code**
- Properties related to text are inherited: `font-family`, `font-size`, `color`, etc;
- The computed value of a property is what gets inherited, **not** the declared value.
- Inheritance of a property only works if no one declares a value for that property;
- The `inherit` keyword forces inheritance on a certain property.
- The `initial` keyword resets a property to its initial value.

## Website Rendering: The Visual Formatting Model

**The Visual Formatting Model** is an algorithm that calculates boxes and determines the layout of these boxes, for each element in the render tree, in order to determine the final layout of the page.

One of the fundamental concepts of CSS.

- **Dimensions of boxes**: the box model;
- **Box type**: inline, block, and inline-block;
- **Positioning scheme**: floats and positioning;
- **Stacking contexts**;
- Other elements in the render tree;
- Viewport size, dimensions of images, etc.

### The Box Model

- **Content**: text, images, etc.
- **Padding**: transparent area arount the content, inside of the box;
- **Border**: goes around the padding and the content;
- **Margin**: space between boxes;
- **Fill area**: area that gets filled with background color or background image

`TOTAL_WIDTH = right border + right padding + specified width + left padding + left border`
`TOTAL_HEIGHT = top border + top padding + specified height + bottom padding + bottom border`

### The Box Model with `box-sizing: border-box`

`TOTAL_WIDTH = specified width`
`TOTAL_HEIGHT = specified height`

**This makes our lives much easier.**

#### Box Types: `inline`, `block-level`, and `inline-block`

##### **Block-level boxes**

- Elements formatted visually as blocks
- 100% of parent's width
- Vertically, one after another
- Box-model applies as shown

```CSS
display: block
(display: flex)
(display: list-item)
(display: table)
```

##### **Inline boxes**

- Content is distributed in lines
- Occupies only content's space
- No line-breaks
- No heights and widths
- Paddings and margins only horizontal (left and right)

```css
display: inline;
```

##### **Inline-block boxes**

- A mix of block and inline
- Occupies only content's space
- No line-breaks
- Box-model applies as shown:

```css
display: inline-block;
```

#### Positioning Schemes: Normal flow, absolute positioning, and floats

##### Normal flow

- Default positioning scheme
- **NOT** floated
- **NOT** absolutely positioned
- Elements laid out according to their source order.

```css
Default
position: relative
```

##### Floats

- **Element is removed from the normal flow**
- Text and inline elements will wrap around the floated element
- The container will not adjust its height to the element.

```css
float: left
float: right
```

##### Absolute Positioning

- **Element is removed from the normal flow**
- No impact on surrounding content or elements
- We use `top, bottom, left, right` to offset the element from its relatively positioned container.

```css
position: absolute
position: fixed
```

#### Stacking Contexts

(see slides), `z-index`

### The "Think - Build - Architect" Mindset

- **Think** about the layout of your webpage or web app before writing code.
- **Build** your layout in HTML and CSS with a consistent structure for naming classes.
- Create a logical **architecture** for your CSS with files and folders.

#### THINK: Component-driven design

- **Modular building blocks** that make up interfaces
- Held together by the **layout** of the page.
- **Re-usable** across a project and between different projects
- **Independent**, allowing us to use them anywhere on the page

Note: Another way of thinking about this is Brad Frost's "Atomic Design"

#### BUILD: BEM -> Block Element Modifier

- **BLOCK**: standalone component that is meaningful on its own.
- **ELEMENT**: part of a block that has no standalone meaning.
- **MODIFIER**: a different version of a block or an element.

```css
.block {
}
.block__element {
}
.block__element--modifier {
}
```

**Low specificity!**

#### ARCHITECT: The 7-1 Pattern

7 different folders for partial Sass files, and 1 main Sass file to import all other files into a compiled stylesheet.

**The 7 Folders**

- `base/`
- `components/`
- `layout/`
- `pages/`
- `themes/`
- `abstracts/`
- `vendors/`

### Basic Responsive Design Principles

1. Fluid Layouts

- To allow webpage to adapt to the current viewport width (or even height)
- Use % (or vh/vw) unit instead of px for elements that should adapt to viewport (usually layout)
- Use max-width instead of width

2. Responsive Units

- Use rem unit instead of px for most lengths
- To make it easy to scale the entire layout down (or up) automatically

3. Flexible Images

- By default, images don't scale automatically as we change the viewport, so we need to fix that
- Always use % for image dimensions, together with the max-width property

4. Media Queries

- To change CSS styles on certain viewport widths (called breakpoints)

**Float Layouts** The old way of building layouts of all sizes, using the float CSS property. Still used, but getting outdated.

**Flexbox** Modern way of laying out elements in a **1-dimensional row** without using floats. Perfect for component layouts

**CSS Grid** For laying out elements in a fully fledged **2-dimensional grid**. Perfect for **page layouts and complex components**.
