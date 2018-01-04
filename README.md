# reveal.js-mermaid-plugin
A very basic [mermaid](https://github.com/knsv/mermaid) plugin for [reveal.js](https://github.com/hakimel/reveal.js)

## How It Works

I preferred not to use markdown in my slides. I found just having mermaid tagged classes messed up with the overall presentation in bad ways. So this plugin adds and removes data for mermaid to process on each slide change. So when slides change, it checks the previous slide and destroys any SVG created by mermaid and creates an svg in the current slide (only if tagged as a diagram slide).

## Usage

1. Copy plugin to your plugin directory (e.g. plugin/mermaid/mermaid.js) and install mermaid js & css (to lib/js & css/ respectively).
2. Reference mermaid javascript, plugin javascript & mermaid css. Example:
```
  <!-- in head -->
  <link rel="stylesheet" href="css/mermaid.css">
  <script src="lib/js/mermaid.full.js"></script>

  <!-- in reveal dependencies -->
      { src: 'plugin/mermaid/mermaid.js' }
```
3. Create diagram in slide. Example:
```
  <section class="diagram-slide">
      <span class="diagram-data" style="display:none;">
graph LR;
  A(AAAA)==> B(B node);
  B==> C(SEE SEE);

  class A diag-a-styles;
  class B diag-b-styles
  class C diag-c-styles;
      </span>
      <!-- Diagram will be displayed in this DIV -->
      <div class="diagram-display">
      </div>
  </section>
```

### CSS classes & Data attributes

This plugin uses CSS classes to figure out what to render.

* "diagram-slide" is used to mark a slide that has a mermaid diagram. Note that you can only have one diagram per slide (it doesn't seem like the audience can really comprehend more than one per slide anyway). This class tagging is used with Reveal's 'slidechanged' event.
* "diagram-data" should tag a span or other element containing the mermaid graph declaration. Note that you probably want to mark that as 'display: none' in your CSS.
* "diagram-display" class should mark an empty div where you want the diagram to appear.

Note: It's possible diagram-data & diagram-div could be combined but I went thru several iterations of making this work reliably.

There are a couple data attributes used.

* The mermaid library itself adds 'data-processed' to the "diagram-div" div after inserting the svg.
* If a slide has the data attribute 'data-diagram-source', then the value will be used to find another slide in the presentation with that value for 'data-state'. The mermaid diagram in that slide will be shown in this slide.

### Styling Diagram Nodes

Mermaid allows you to give a CSS class name to a node (see example above). In your presentation head you can thus setup styles for your diagrams:

```
  <link rel="stylesheet" href="css/mermaid.css">
  <script src="lib/js/mermaid.full.js"></script>
  
  <!-- mermaid diagram related styles -->
  <style>

    .diag-a-style rect {
      stroke: blue !important;
      stroke-width: 3px !important;
    }

    .diag-a-style .label {
      color: black !important;
    }

    ...
  </style>

```

## Things to Improve

* Creating a larger diagram seems to be hard. Reveal seems to allocate a fairly small box to the diagram. In my presentation using this, I hacked around this by using CSS:
```
    .reveal .diagram-display {
      padding: 0;
      margin: 0;
      min-height: 700px;
      min-width:100%;
      height:auto;
    }
```
* Alternately, it would be nice to make the dynamically generated SVG nodes as the slide background. I got part way there -- flash of the diagram -- but then it would disappear. I haven't had a chance to debug.
* It would be nice to figure out why just tagging the mermaid graph declaration and letting mermaid javascript handle it doesn't work (it caused the presentation to entirely break for me).
* There's an egregious hack in the javascript regarding SVG resizing (see function fixUpSvgSize). But my knowledge of CSS, Javascript & SVG is such that I don't know how to fix it any better.
