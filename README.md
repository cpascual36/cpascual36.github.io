## Project 4: Website Performance Optimization 

Goal:  Given the provided website, optimize the critical rendering path and make the page render as quickly as possible. Identify and resolve performance issues to achieve a target score of 90+ for index.html on PageSpeed Insights mobile/desktop and a consistent 60 FPS when scrolling in pizza.html. 

###File Structure
<h6>src/</h6>
Contains development CSS, JS, and images, sorted into respective directories.
<h6>dest/</h6>
Contains the production ready CSS, JS, and images built from the src/ files.

###Original Files
https://github.com/udacity/frontend-nanodegree-mobile-portfolio

###Optimization

####Index Page
The original PageSpeed Insights score for index.html was 35/100 for mobile and 47/100 for desktop. After optimizing, the scores are 95 for mobile and 96 for desktop. The following changes were made to to achieve these scores:

<h5>Images</h5>
1. Resized and compressed pizzeria.jpg using Photoshop.
2. Losslessly compress profilepic.jpg using Photoshop.

<h5>CSS</h5>
1. For the main stylesheet, inline CSS into index.html using Gulp with Gulp-Inline plugin. This improves performance because external style sheets referenced in the head are render blocking. Inlining this small style sheet allowed the browser to proceed with rendering the page.
2. For the print stylesheet, add the media query "print" to the script tag so that it does not block rendering.

<h5>JS</h5>
1. Because Google Analytics was not critical to the initial render of the page, the async attribute was added to the script tag. Making the script asynchronous allows the browser to render the page without waiting for the download and execution of the external script.

####Cam's Pizzeria
Cam's Pizzeria page originally had FPS rate of less than 30. Chrome DevTools was used to measure the FPS timeline and debugging/optimizing the javascript. There were two major bottlenecks.
<h5>JS</h5>
1. function updatePositions()
Original
'''
function updatePositions() {
  frame++;
  window.performance.mark("mark_start_frame");

  var items = document.querySelectorAll('.mover');
  for (var i = 0; i < items.length; i++) {
    var phase = Math.sin((document.body.scrollTop / 1250) + (i % 5));
    items[i].style.left = items[i].basicLeft + 100 * phase + 'px';
  }
'''

Optimized:  
Removed scrollTop from for loop by caching the value before. Requests for style information should be minimized because they prevent the browser from optimizing reflows (Stoyan's phpied.com).
Also replaced querySelectorAll with getElementsByClassName because it is faster (NCZonline).
'''
function updatePositions() {
  frame++;
  window.performance.mark("mark_start_frame");
  var items = document.getElementsByClassName('mover');
  var cachedScrollTop = (document.body.scrollTop/1250);
  var len = items.length;
  for (var i = 0; i < len; i++) {
  var phase = Math.sin(cachedScrollTop + (i % 5));
  items[i].style.left = items[i].basicLeft + 100 * phase + 'px';
  }
'''
  
2. function changePizzaSizes(sizes)
Original
'''
  function changePizzaSizes(size) {
    for (var i = 0; i < document.querySelectorAll(".randomPizzaContainer").length; i++) {
      var dx = determineDx(document.querySelectorAll(".randomPizzaContainer")[i], size);
      var newwidth = (document.querySelectorAll(".randomPizzaContainer")[i].offsetWidth + dx) + 'px';
      document.querySelectorAll(".randomPizzaContainer")[i].style.width = newwidth;
    }
  }
'''
Optimized:  
Moved variables out of the for loop to prevent unnecessary calculations. Also replaced querySelectorAll with getElementsByClassName because it is faster (NCZonline).
'''
  function changePizzaSizes(size) {
  	  var dx = determineDx(document.getElementsByClassName("randomPizzaContainer")[1], size);
      var newwidth = (document.getElementsByClassName("randomPizzaContainer")[1].offsetWidth + dx) + 'px';
  	  var pizzacount = document.getElementsByClassName("randomPizzaContainer").length;

    for (var i = 0; i < pizzacount; i++) {
     document.getElementsByClassName("randomPizzaContainer")[i].style.width = newwidth;
   }

####Additional Optimizations
1. Reduce the number of pizzas from 200 to 25. The page still appears sufficiently full of pizza and yet reduces the number of items to be rendered.
2. Remove height and width from generated pizza elements and move width to CSS.
3. Edited and compressed pizza.png. The height of the image was far larger than width but only contained empty space.

####Sources

* [NCZonline](https://www.nczonline.net/blog/2010/09/28/why-is-getelementsbytagname-faster-that-queryselectorall/).
* [Stoyan's phpied.com](http://www.phpied.com/rendering-repaint-reflowrelayout-restyle/)
* [Optimizing Performance](https://developers.google.com/web/fundamentals/performance/ "web performance")
* [Analyzing the Critical Rendering Path](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/analyzing-crp.html "analyzing crp")
* [Optimizing the Critical Rendering Path](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/optimizing-critical-rendering-path.html "optimize the crp!")
* [Avoiding Rendering Blocking CSS](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/render-blocking-css.html "render blocking css")
* [Optimizing JavaScript](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/adding-interactivity-with-javascript.html "javascript")
* <a href="https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/image-optimization.html">Optimize images</a>


