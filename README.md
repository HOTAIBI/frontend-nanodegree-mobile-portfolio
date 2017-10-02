## Website Performance Optimization portfolio project

This Project To optimize this online portfolio for speed. In particular, optimize the critical rendering path and make this page render as quickly as possible by applying the techniques you've picked up in the [Critical Rendering Path course](https://www.udacity.com/course/ud884).

#### Part 1: Optimize PageSpeed Insights score for index.html

The following steps used to Optimize Page Speed index.html:

1. Optimize CSS delivery by inline style.
2. Optimize JS delivery by "Async" attribute, and add scripts before end body tag.
3. Minify JS, Html, images files.
4. Enable and test gzip compression support on your web server (Apache: [Use mod_deflate](http://httpd.apache.org/docs/current/mod/mod_deflate.html)).

#### Part 2: Optimize Frames per Second in pizza.html

To optimize views/pizza.html, I modify views/js/main.js until My frames per second rate is 60 fps or higher as Follow:
1. In line 490, I solve Forced reflow problem
~~~~js
  var items = document.getElementsByClassName('mover');
  // document.body.scrollTop is no longer supported in Chrome.
    var scrollTop = document.documentElement.scrollTop || document.body.scrollTop;
var itemsLength= items.length;
  for (var i = 0; i < itemsLength; i++) {
    var phase = Math.sin((scrollTop / 1250) + (i % 5));
    items[i].style.left = items[i].basicLeft + 100 * phase + 'px';
  }
~~~~
2.In line 511, The number of background pizzas should be reduced to at least 24 (3 full lines of 8 background pizzas). A better approach is to dynamically calculate the number of pizzas needed to fill the screen, based on browser window resolution.
~~~~js
window.addEventListener('scroll', updatePositions);
var s = 256;
  var cols = screen.width / s;
  var rows = screen.height / s;
  var total = cols * rows;
// Generates the sliding pizzas when the page loads.
/** Outside the loop **/
var movingPizzas = document.getElementById('movingPizzas1');
document.addEventListener('DOMContentLoaded', function() {
  for (var i = 0, elem; i < total; i++) {
     elem = document.createElement('img');
    elem.className = 'mover';
    elem.src = "images/pizza.png";
    elem.style.height = "100px";
    elem.style.width = "73.333px";
    elem.basicLeft = (i % cols) * s;
    elem.style.top = (Math.floor(i / cols) * s) + 'px';
    movingPizzas.appendChild(elem);
  }
  updatePositions();
});
~~~~
3. Declaring the elem variable (var elem;) in the initialisation of the for-loop will prevent it from being created every time the loop is executed.
~~~~js
  for (var i = 0, elem; i < total; i++) {
     elem = document.createElement('img');
    //Other statements
}
~~~~
4. The document.getElementById() Web API call is faster. I move this DOM call outside the for statement and save it into a local variable, which can be used here.
~~~~js
// Outside the loop 
var movingPizzas = document.getElementById('movingPizzas1');

//Inside the for statement / loop – this line
movingPizzas.appendChild(elem);
~~~~
  

### Optimize resize pizza in pizza.html
to improve the time to resize pizzas is less than 5 ms using the pizza size slider on the views/pizza.html page.  I used the following steps:
1. "sizeSwitcher" Function out "determineDx". "determineDx" is deleted to minimize memory storage and improve performance
~~~~js
changeSliderLabel(size);
  function sizeSwitcher (size) {
    switch(size) {
      case "1":
        return 0.25;
      case "2":
        return 0.3333;
      case "3":
        return 0.5;
      default:
        console.log("bug in sizeSwitcher");
    }
  }
~~~~
2. Update changePizzaSizes function (line 443) To avoide Forced reflow problem
~~~~ js
  // Iterates through pizza elements on the page and changes their widths
  function changePizzaSizes(size) {
    var PizzaContainaer= document.querySelectorAll(".randomPizzaContainer");
    var windowWidth = document.getElementById("randomPizzas").offsetWidth;
      var offsetWidth = PizzaContainaer[0].offsetWidth;
    for (var i = 0; i < PizzaContainaer.length; i++) {
      var dx = (sizeSwitcher(size) - (offsetWidth / windowWidth)) * windowWidth;;
      var newwidth = (offsetWidth + dx) + 'px';
      PizzaContainaer[i].style.width = newwidth;
    }
  }
~~~~
3. I used document.getElementById() Web API call instead document.querySelector().
4. The document.getElementsByClassName() Web API call instead document.querySelectorAll().
5. I save the array length, which is part of the condition statement, in a local variable, so the array's length property is not accessed to check its value at each iteration. (i.e. more efficiency)
6. I declare the "pizzasDiv" variable outside the loop, so only DOM call is made one (Line 457).
~~~~ js
var pizzasDiv = document.getElementById("randomPizzas");
for (var i = 2; i < 100; i++) {
  pizzasDiv.appendChild(pizzaElementGenerator(i));
}
~~~~ 

