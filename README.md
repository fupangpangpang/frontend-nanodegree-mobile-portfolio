# Web Optimization Project


##Getting Started

**1.** Clone this repo:

```
$ git clone https://github.com/fupangpangpang/frontend-nanodegree-mobile-portfolio
````

**2.** Serve the website:

```
$ python -m SimpleHTTPServer 8000
```

**3.** Open the website:

```
$ open "http://localhost:8000"
```


##Optimization


###Sliding Pizzas

The following changes where made to fix the low FPS and produce a consistent 60FPS frame rate when scrolling the page:

######Reduced Pizza Elements

Reduced the amount of sliding pizza elements generated from 200 down to 30, which still sufficiently fills the screen with sliding pizzas.

```js
document.addEventListener('DOMContentLoaded', function() {
  var cols = 8;
  var s = 256;
  for (var i = 0; i < 30; i++) {
    var elem = document.createElement('img');
    elem.className = 'mover';
    elem.src = "images/pizza.png";
    elem.style.height = "100px";
    elem.style.width = "73.333px";
    elem.basicLeft = (i % cols) * s;
    elem.style.top = (Math.floor(i / cols) * s) + 'px';
    document.querySelector("#movingPizzas1").appendChild(elem);
  }
  updatePositions();
});
```

######Improved Efficiency

Moved the calculation which utilizes the scrollTop method outside of the loop.

```js
function updatePositions() {
  frame++;
  window.performance.mark("mark_start_frame");

  var items = document.querySelectorAll('.mover');
  var scrollTopN = document.body.scrollTop / 1250
  for (var i = 0; i < items.length; i++) {
    var phase = Math.sin(scrollTopN + (i % 5));
    items[i].style.left = items[i].basicLeft + 100 * phase + 'px';
  }

  // User Timing API to the rescue again. Seriously, it's worth learning.
  // Super easy to create custom metrics.
  window.performance.mark("mark_end_frame");
  window.performance.measure("measure_frame_duration", "mark_start_frame", "mark_end_frame");
  if (frame % 10 === 0) {
    var timesToUpdatePosition = window.performance.getEntriesByName("measure_frame_duration");
    logAverageFrame(timesToUpdatePosition);
  }
}
```


###Resized Pizzas

The following changes were made to resize the pizzas in under 5ms:

######Improved Efficiency

Remove determineDx function and replace it with simpler version of relative width. Created a new variable to hold all of the .randomPizzaContainer elements in the document and looped through the elements to apply the new width value.


```js

  function changePizzaSizes(size) {

        // Changes the slider value to a percent width
      switch(size) {
        case "1":
          newwidth =  25;
          break;
        case "2":
          newwidth = 33.33;
          break;
        case "3":
          newwidth = 50;
          break;
        default:
          console.log("bug in sizeSwitcher");
      }
    var randomPizzas = document.querySelectorAll(".randomPizzaContainer")
    for (var i = 0; i < randomPizzas.length; i++) {
      randomPizzas[i].style.width = newwidth + "%";
    }
  }

```

