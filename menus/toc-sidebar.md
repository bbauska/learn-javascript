## HTML Table of Contents Sidebar using CSS & JS

The table of content is a useful element to navigate between the sections of a longer article. It is also a useful 
way to provide a summary of topics to the users containing in an article. Users can easily jump to the selected 
topic with the help of a table of contents. So, how you can create a TOC in HTML? well! this tutorial explains 
the method to develop a table of contents in the sidebar using HTML, CSS, and JavaScript.

Before creating the TOC, check the real example of the fixed sidebar table of contents on the demo page. There 
you can scroll the article and see how topics indicated with an animated marker. Similarly, you can scroll to 
the topic by clicking on the title of the topic.

Although, it’s an animated table of contents, the weak point is that you need to create its HTML structure 
manually. Because the JS file only handles marker/indicator animation and doesn’t generate TOC links dynamically. 
Anyhow, it’s quite fit for single-page applications/pages.

HTML Structure for Table of Contents Sidebar
Create the HTML nav element with a class name "toc" and place a list of hash links following the id of article 
sections. At the end of the list, place the svg element for the marker that indicates the active item in the 
table of content.

```
<nav class="toc">
   <ul>
      <li><a href="#topic1">Topic 1</a></li>
      <li>
         <a href="#topic2">Topic 2</a>
         <ul>
            <li><a href="#itemA">Item A</a></li>
            <li><a href="#itemB">Item B</a></li>
            <li><a href="#itemC">Item C</a></li>
            <li><a href="#itemD">Item D</a></li>
         </ul>
      </li>
      <li>
         <a href="#css">Topic 3</a>
         <ul>
            <li><a href="#itemE">Item E</a></li>
            <li><a href="#itemF">Item F</a></li>
            <li><a href="#itemG">Item G</a></li>
         </ul>
      </li>
   </ul>
   <svg class="toc-marker" width="200" height="200" xmlns="http://www.w3.org/2000/svg">
      <path stroke="#444" stroke-width="3" fill="transparent" stroke-dasharray="0, 0, 0, 1000" stroke-linecap="round" stroke-linejoin="round" transform="translate(-0.5, -0.5)" />
   </svg>
</nav>
```

Similarly, create the article element with the class name "contents" and write your article inside it. You can 
create multiple sections with a unique id that you can target in the TOC’s link.

```
<article class="contents">
   <section id="topic1">
      <h2>Topic 1</h2>
      <p>
         Some text.
      </p>
      <h2>Slides for Developers</h2>
      <p>
         Some text.
      </p>
      <p>
         Some text.
      </p>
   </section>
   <div id="topic2">
      <h2>Topic 2</h2>
      <p>
         Next topic is discussed here!
      </p>
      <p>
         Some text.
      </p>
   </div>
</article>
```

# Style TOC with CSS
After creating the HTML structure, now it’s time to style the TOC with CSS. So, target the “toc” class and set its width, height, and 
background color. In order to place the table of contents into the sidebar, use the CSS fixed property and set the left value 0. Likewise, 
set the top value (6em by default) according to your needs.

```
.toc {
  position: fixed;
  background: #ccc;
  left: 0;
  top: 6em;
  padding: 1em;
  width: 14em;
  height: 100vh;
  line-height: 2;
}
```

Now, target the ul element of the toc and remove its default bullets using list-style none property. 
Likewise, define the CSS styles for the links as follows:

```
.toc ul {
  list-style: none;
  padding: 0;
  margin: 0;
}
.toc ul ul {
  padding-left: 2em;
}
.toc li a {
  display: inline-block;
  color: #aaa;
  text-decoration: none;
  transition: all 0.3s cubic-bezier(0.23, 1, 0.32, 1);
}
.toc li.visible > a {
  color: #111;
  transform: translate(5px);
}
```

The "toc-marker" class is the indicator for the current topic. Define its width, height, and set its absolute position.

```
.toc-marker {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: -1;
}
.toc-marker path {
  transition: all 0.3s ease;
}
```

efine the basic styles for content/article. You can set the custom styles for the article or use the existing style of your current theme.

```
.contents {
  padding: 1em;
  max-width: 800px;
  font-size: 1.2em;
  font-family: "Frank Ruhl Libre", sans-serif;
}
.contents img {
  max-width: 100%;
}
.contents .code-block {
  white-space: pre;
  overflow: auto;
  max-width: 100%;
}
.contents .code-block code {
  display: block;
  background-color: #f9f9f9;
  padding: 10px;
}
.contents .code-inline {
  background-color: #f9f9f9;
  padding: 4px;
}
.contents h2,
.contents h3 {
  padding-top: 1em;
}
```

TOC JavaScript Function
In the final step, place the following JavaScript code at the end of the article wrapping with <script> tag.

```
var toc = document.querySelector( '.toc' );
var tocPath = document.querySelector( '.toc-marker path' );
var tocItems;
// Factor of screen size that the element must cross
// before it's considered visible
var TOP_MARGIN = 0.1,
    BOTTOM_MARGIN = 0.2;
var pathLength;
var lastPathStart,
		lastPathEnd;
window.addEventListener( 'resize', drawPath, false );
window.addEventListener( 'scroll', sync, false );
drawPath();
function drawPath() {
  tocItems = [].slice.call( toc.querySelectorAll( 'li' ) );
  // Cache element references and measurements
  tocItems = tocItems.map( function( item ) {
    var anchor = item.querySelector( 'a' );
    var target = document.getElementById( anchor.getAttribute( 'href' ).slice( 1 ) );
    return {
      listItem: item,
      anchor: anchor,
      target: target
    };
  } );
  // Remove missing targets
  tocItems = tocItems.filter( function( item ) {
    return !!item.target;
  } );
  var path = [];
  var pathIndent;
  tocItems.forEach( function( item, i ) {
    var x = item.anchor.offsetLeft - 5,
        y = item.anchor.offsetTop,
        height = item.anchor.offsetHeight;
    if( i === 0 ) {
      path.push( 'M', x, y, 'L', x, y + height );
      item.pathStart = 0;
    }
    else {
      // Draw an additional line when there's a change in
      // indent levels
      if( pathIndent !== x ) path.push( 'L', pathIndent, y );
      path.push( 'L', x, y );
      // Set the current path so that we can measure it
      tocPath.setAttribute( 'd', path.join( ' ' ) );
      item.pathStart = tocPath.getTotalLength() || 0;
      path.push( 'L', x, y + height );
    }
    pathIndent = x;
    tocPath.setAttribute( 'd', path.join( ' ' ) );
    item.pathEnd = tocPath.getTotalLength();
  });
  pathLength = tocPath.getTotalLength();
  sync();
}
function sync() {
  var windowHeight = window.innerHeight;
  var pathStart = pathLength,
      pathEnd = 0;
  var visibleItems = 0;
  tocItems.forEach( function( item ) {
    var targetBounds = item.target.getBoundingClientRect();
    if( targetBounds.bottom > windowHeight * TOP_MARGIN && targetBounds.top < windowHeight * ( 1 - BOTTOM_MARGIN ) ) {
      pathStart = Math.min( item.pathStart, pathStart );
      pathEnd = Math.max( item.pathEnd, pathEnd );
      visibleItems += 1;
      item.listItem.classList.add( 'visible' );
    }
    else {
      item.listItem.classList.remove( 'visible' );
    }
  });
  // Specify the visible path or hide the path altogether
  // if there are no visible items
  if( visibleItems > 0 && pathStart < pathEnd ) {
    if( pathStart !== lastPathStart || pathEnd !== lastPathEnd ) {
      tocPath.setAttribute( 'stroke-dashoffset', '1' );
      tocPath.setAttribute( 'stroke-dasharray', '1, '+ pathStart +', '+ ( pathEnd - pathStart ) +', ' + pathLength );
      tocPath.setAttribute( 'opacity', 1 );
    }
  }
  else {
    tocPath.setAttribute( 'opacity', 0 );
  }
  lastPathStart = pathStart;
  lastPathEnd = pathEnd;
}
```

That’s all! Hopefully, you have successfully created a table of contents in HTML. If you have any questions or need further help, let me know by comment below.
