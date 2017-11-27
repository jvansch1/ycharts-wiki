# YCharts Frontend Development for 2018

## Technologies

### Sass

Sass is a CSS framework that brings common engineering concepts of state and modularity to style sheets. It will allow us to decompose our massive CSS files into smaller file hierarchies to better match the semantics and nested structure of our markup.

### Webpack

Webpack is a replacement for Django Pipeline. It runs via Node, has plugin and Babel support, a clean configuration interface and is well established. It's designed for complex JS apps like our own.

### Angular 1.5

Some work on moving us to Angular 1.5 has already been done and latest thinking can be seen here: [[Road-to-AngularJS-2.0]]. However, in the context of our other front end technology migrations, a revisit is required. Since Webpack runs via node and has support for Babel, imaginably, we can write all future 1.5 code in ES6+.

## Development Plan

### Discussion

In theory, each of these technologies can be integrated into YCharts independently(sequenced in isolation, different timelines, etc). However, especially since Webpack allows ES6+ and takes as input Sass and Angular 1.5 files, it might make sense to bundle efforts.

### Step 0 Effort and Project Validation
- Convert systems app to use Webpack.
- When we get Sass from css person, integrate into app + use webpack in that app.
- Convert systems app angular to ES6+

### Longterm Development Plan Ideas
- Webpack work can be sequenced whenever
- integrating Sass into apps should happen with webpack conversion
- Angular 1.5 conversion can also happen whenver, but if we want ES6+, then we should sequence after Webpack conversion.

