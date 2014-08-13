# Spartan

A highly customizable LessCSS grid system. Try the [dynamic online demo](http://spartan-grid.herokuapp.com/).

> Requires [LessCSS](https://github.com/less/less.js) version 1.4.0 or above. Does **not** work with v1.7.0 
because of [this bug](https://github.com/less/less.js/pull/1929#issuecomment-45307325) (fixed in v1.7.1).

Spartan arose from a project where we had the need for a fluid responsive grid system with both html and less integration possibilities. As we couldn't find anything covering the requirements at that time we simply developed something on our own. Over time the initial idea developed into this vastly enhanced less grid system.

- [Features](#features)
- [Comparison with other Grids](#comparison)
- [Getting Started](#getting-started)
- [Grid Structure](#grid-structure)
- [Basic Usage](#basic-usage)
	- [Grid Setup](#grid-setup)
	- [Grid Classes and Mixins](#grid-classes-and-mixins)
- [Responsiveness and Customization](#responsiveness-and-customization)
- [Browser Support](#browser-support)
	- [The Safari Problem](#the-safari-problem)

## Features

- supports all features of other common grid systems:
	- fluid or static grid
	- fully responsive
	- block gutter (fixed gutter in a fluid grid)
	- column reordering through css
	- column offsets
- grid creation API for maximum flexibility
- customizable css classes
- tiny: **~11KB** of LessCSS which generates less than **2KB** of base css (minified)

## Comparison

> Install [Chromoji](https://chrome.google.com/webstore/detail/chromoji-emoji-for-google/cahedbegdkagmcjfolhdlechbkeaieki?hl=en-GB) if you're using Chrome to see the icons.

|                           | [Spartan](http://spartan-grid.herokuapp.com) | [Bootstrap](http://getbootstrap.com/css/#grid) | [Foundation](http://foundation.zurb.com/) | [Profound](http://www.profoundgrid.com/) |
|---------------------------|:-------:|:-----------------:|:----------:|:-------:|
| less                      | ✅ | ✅ | ❌ | ❌ |
| sass                      | ❌ | ❌ | ✅ | ✅ |
| fluid                     | ✅ | ✅ | ✅ | ✅ |
| static                    | ✅ | ✅ | ✅ | ✅ |
| responsive                | ✅ | ✅ | ✅ | ✅ |
| block gutter              | ✅ | ✅ | ✅ | ❌ |
| column ordering           | ✅ | ✅ | ✅ | ✅ |
| offsets                   | ✅ | ✅ | ✅ | ✅ |
| multiple grids            | ✅ | ❌ | ❌ | ✅ |
| custom classes            | ✅ | ❌ | ❌ | ✅ |
| output css (min)          | **1.8-5KB** | **8.3KB** | **15.1KB** | **~10KB** |

## Getting Started

- Download Spartan
- Install [Node](http://nodejs.org/) and then LessCSS: `npm install -g less`
- Adjust `grid-setup.less`
- Execute: `lessc compile.less grid.css`

## Grid Structure

Here's a short example of how a Spartan grid looks like with a responsive layout implementation:

**HTML**

```html
<div class="g-row g-layout-1">
	<div class="g-col"></div>
	<div class="g-col"></div>
	<div class="g-col"></div>
	<div class="g-col"></div>
</div>
```

**LessCSS**

```less
.g-layout-1 {
	// maximum columns: 12
	// omit small media definition as it uses full width anyway
	
	// 2 columns in medium screen
	@media @medium {
		.grid-col-set-equal(6);
	}
	// 3 columns in large screen
	@media @large {
		.grid-col-set-equal(4);
	}
}
```

Spartan uses a negative left margin on `.g-row` and left padding (gutter) on `.g-col`s for having a consistent structure and reduced output css.

Check out [examples](https://github.com/SimonHarte/SpartanGrid/blob/master/examples/responsive-layouts.less) for more responsive layouts.

## Basic Usage

### Grid Setup

There are two ways to set up a grid with Spartan, within the basic usage we only explain the simpler one. If you need more flexibility, to set up viewport dependent grids etc. go to the [advanced usage](#flexible-setup-mixins) section.

The basic grid setup requires you to call just one mixin, pass in a configuration and an optional prefix (read more about [prefixing](#prefixing)).

```less
@grid-config: [grid-type], [grid-width], [gutter-width], [grid-type], [grid-columns];

.grid-bundle([prefix], @grid-config);
```

If you are not using a grid prefix, you have to name the config param, so `.grid-bundle()` correctly receives the configuration and doesn't think it's the prefix.

```less
.grid-bundle(@config: @grid-config);
```

#### Setup Params

| Param | Type | Value | Comment |
|-------|:-----|:------|:--------|
| `prefix`       | expression | `~''` or custom | Example: `grid-` (without single quotes) |
| `grid-type`    | string  | `'fluid'` or `'fixed'` | Single quotes mandatory |
| `grid-width`   | pixel   | | |
| `gutter-width` | pixel   | | |
| `gutter-type`  | string  | `'fluid'` or `'fixed'` | Forced to `'fixed'` if `grid-type` is `'fixed'` |
| `grid-columns` | integer | | | |

#### Example Setup and Markup

**LessCSS**

```less
@grid-config: 'fluid', 940px, 20px, 'fixed', 12;

.grid-bundle(@config: @grid-config);
```

**HTML**

```html
<!-- defining two columns in the current grid (max columns: 12) -->
<div class="g-row">
	<div class="g-col g-span-6"></div>
	<div class="g-col g-span-6"></div>
</div>
```

- `.g-row` initializes a grid row and uses [H5BP clearfix](https://github
.com/h5bp/html5-boilerplate/blob/master/css/main.css#L161) to contain the floating `.g-col`s.
- `.g-col` applies default column styles like float and padding (gutter).
- `.g-span-[columns]` applies a column based width according ot the set maximum amount of columns.

### Grid Classes and Mixins

Spartan comes with an integrated LessCSS loop to generate grid classes according to your configuration. Instead of using
the generated classes you can also use similar named mixins to apply the grid in a less style sheet.

```less
.grid-span([columns]), .g-span-[columns]
.grid-offset([columns]), .g-offset-[columns]
.grid-push([columns]), .g-push-[columns]
.grid-pull([columns]), .g-pull-[columns]
```

#### `grid-span`
Override the width of a column (default is full-width), equivalent to `.g-span-[columns]`. This mixin allows you to pass
in floating point numbers, which enables you to define literally **any** width using the mixin.

```less
.grid-span(12/5);
```

#### `grid-offset`
Indent a column by the defined amount of grid columns, equivalent to `.g-offset-[columns]`.
While there are just positive indents in generated classes, you could also call this mixin with negative values.

> **Note:** Using negative indents generally only works in static width grids.

#### `grid-push` and `grid-pull`
Together with the additional class `.g-reorder` on `.g-row` you can reorder columns visually without changing the 
order in HTML. Note that you may run into problems if these are used within certain responsive layouts as they use 
relative positioning. Equivalent to `.g-push-[columns]` and `.g-pull-[columns]`, similar behavior as `.grid-offset()`.

##### `grid-reorder`

You can alternatively use `.grid-reorder()` and pass in any number. If the passed number is positive or zero, it will
simply call `.grid-push()` and if the number is negative `.grid-pull()` instead with the absolute value of the number.

## Responsiveness and Customization

There's a detailed [documentation](https://github.com/SimonHarte/SpartanGrid/blob/master/Documentation.md) about every aspect of Spartan with different approaches to setting up your most flexible grid.

## Browser Support

**Not** supported:

- **IE7-**

**Partially** supported:

- **IE8**: basic grid working properly, no media query support and :nth-child clearing, 
**Note:** fully supported with [respond.js](https://github.com/scottjehl/Respond) and [selectivizr.js]
(http://selectivizr.com/) polyfills
- **Safari**: as explained [below](#the-safari-problem)

### The Safari Problem

Unfortunately all Safari browsers up to the latest version have a bad subpixel rendering for widths and always round 
values down to the next integer when calculating rendered pixels. So a width declaration of for example `88.333px` or
 even `88.666px` will always be rendered as `88px`. This of course presents an issue when your grid configuration 
 leads to such values or in general when you use a fluid grid, because we get a small displacement of columns.

There's an [article from John Resig](http://ejohn.org/blog/sub-pixel-problems-in-css/) about this subject.

Note that all grid systems suffer from this issue and merely provide workarounds in some cases. Spartan does not try to do that
~~but instead there is an alternate grid style pattern with [Spartan 2](https://github.com/SimonHarte/Spartan2) which
uses the same techniques as Profound to position each grid column independently with margins because they are not
affected by this subpixel rendering issue.~~ (not maintained since v3.0.0)

## Contributors

- Simon Harte (s.harte@gmx.ch)
- Marc Diethelm (marc@web5.me)
