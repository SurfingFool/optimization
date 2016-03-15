## Website Performance Optimization portfolio project

The goal of this project was to optimize the critical rendering path and make index.html page render as quickly as possible and get JavaScript animation on pizza.html to run at a smooth 60 fps.

### Getting started

#### Improving PageSpeed score
In order to see what I was working with, I needed to measure the page load speed score using Google's PageSpeed Insights.  After downloading the original repository on my local drive, I pushed the project files and folders to my GitHub site and set it up to run through a server with its own url which I could then enter into PageSpeed. 

##### Before score = 28/100

**Images**
1. First, I optimized the images by reducing the size of the pizzeria.jpg and profilepic.jpg images and compressed them further using Adobe Photoshop.

**index.html**
2. Commented out/Removed web font.

3. I eliminated render-blocking CSS "above the fold" by inlining all styles in the head section and added a media query for print.css so it would not load unless printed.

4. Eliminated render-blocking JS by adding "async" directly after "script" in the script tags in order to asynchronously load the JS files.

##### After scores: Mobile = 95; Desktop = 96


#### Improving Frames per Second

Achieving 60 fps for the background pizza animation involved measuring the current speed using the recorded timeline in Chrome Dev Tools.  Most of the issues were inside the updatePositions() function:

```
function updatePositions()
```
1. First, I changed:
	`var items = document.querySelectorAll(.mover);`
	to
	`var items = document.getElementsByClassName('mover');`

2. The for loop was causing a math calculation for every iteration, so I moved the math calculation outside the loop. I also attempted to increase speed by caching the value of the items array and reducing the number of elements from 200 to 32.
```javascript
function updatePositions() {
  frame++;
  window.performance.mark("mark_start_frame");

  var items = document.getElementsByClassName('mover');
  // Moved math calculation out of for Loop.
  var top = (document.body.scrollTop / 1250);
  // Cached the value of the items array, so the length of array is calculated just once, and
  // Reduced number of elements in array from 200 to 32.
  for (var i = 32; i--; ) {
    var phase = Math.sin(top + (i % 5));
    items[i].style.left = items[i].basicLeft + 100 * phase + 'px';
  }
```

I then worked on the document.addEventListener function():
```
document.addEventListener('DOMContentLoaded', function() {
```
1. I removed both the height and width styles here, and in CSS file:
```javascript
document.addEventListener('DOMContentLoaded', function() {
  var cols = 8;
  var s = 256;

  for (var i = 32; i--;) {
    var elem = document.createElement('img');
    elem.className = 'mover';
    //Optimized pizza image - reduced size to 100px X 100px. Removed height & width styles; width in CSS.
    elem.src = "images/pizza_small.png";
    elem.basicLeft = (i % cols) * s;
    elem.style.top = (Math.floor(i / cols) * s) + 'px';
    document.querySelector("#movingPizzas1").appendChild(elem);
  }
  updatePositions();
});
```

#### Improving time to resize pizzas

Finally, I worked on decreasing the time it took to resize pizzas when using the slider control inside resizePizzas function.

1. Removed all determineDx function and all associations because knowing the dimensions was not necessary to change the sizes.

2. Changed the changePizzaSizes function by redifining "newWidth".

3. Added pizzaSizes variable and assigned value to prevent repetition.
```
function changePizzaSizes(size) {
    switch(size) {
      case "1":
        newWidth = 25;
        break;
      case "2":
        newWidth = 33.3;
        break;
      case "3":
        newWidth = 50;
        break;
      default:
        console.log("bug in sizeSwitcher");
    }
    // Added variable and assigned value to prevent repetition.
    var pizzaSizes = document.querySelectorAll(".randomPizzaContainer");
    
    for (var i = 0; i < pizzaSizes.length; i++) {
      pizzaSizes[i].style.width = newWidth + "%";
    }
  }

  changePizzaSizes(size);
```
 