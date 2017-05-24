# Illustrator-to-fully-interactive-graphic

Ever done this? "I love working with Illustrator...but I need an interactive file!"

Fear not! There is a way to make an Illustrator file interactive. It's not an everyday route I'd recommend, but if you're in a pickle, you can use this trick to add an interactive element:

1. Create your Illustrator file, perhaps with hidden layers, and save as an SVG.
2. Create a webpage that will load the svg, using JQuery. 
3. Either using the name of the layer — which is the id of that section of the SVG — or using a class that you've added, use JQuery to trigger an event by click or hover. 

## Step one: Create your Illustrator file
Create your Illustrator file as you would normally at first, keeping track of the layers of the document:

If you decide to use the hidden layer route, you will create a hidden layer now. You make hide a layer by clicking on the eye next to the layer name.

Next, go to "Save As" and select SVG. I used the default save settings -- the other ones are at your own risk.
This will create an SVG with a series of grouped paths, shapes, and text. Mine happened to also have a <title> tag, because of the metadata I transferred over from an earlier version of the graphic. Go [here](https://www.w3schools.com/graphics/svg_intro.asp) for more information about SVGs.

## Load the Illustrator file with JQuery 
Create a basic HTML file, and add this code in to the head:
```
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.2.1/jquery.min.js"></script>
```
...or whatever JQuery library you prefer. 

Now add this to the body of the document:
```
$.get('Layered_pie_charts_9-01.svg', function(data) {
  $(body).append(data.documentElement);
  init();
});
```
This will load the SVG, creating what looks like a basic flat image. You'll notice that it resizes in your browser window, though. You can append it to the body or to a specific class or tag.

Now, on to the interactivity.


## Option one: hidden layers 
Did you add in those hidden layers to your Illustrator file? Great. Now here's what it did: it added a class to each of those hidden documents -- in my case, `st132` -- and in the CSS, added some style to it: `.st132{display:none;}`. If you've worked with CSS and JQuery before, you might be familiar with this next step.

The ID of the layer is just what you named it in Illustrator. To reveal a single layer, you can do this:
```
  $('#click-on-this').on('click', function(){
    $('#hidden-layer').show();
```
This JQuery-based code registers when you click on element with a certain ID, finds the #hidden-layer element, and shows it.

You can do the same thing with a class name, if you have multiple elements that you want to trigger the click:
```
  $('.click-class').on('click', function(){
    $('#hidden-layer').show();
```
[This tutorial](http://tomgermeau.com/2014/02/how-designers-can-create-interactive-prototypes-with-illustrator/) has another example of how you can do this.

I wanted something more elaborate: for the graphic to reveal a different hidden element, depending on what the user clicked on. There are many ways to do this, but here's the one I used:
```
function init() {
  $('.org-layer').on('click', function(){
    var id = this.id
    $('#' + id + '_x5F_zoom').show();
    $('.st134').click(function(){
      $('#' + id + '_x5F_zoom').hide();
    });
  });
  ```
What's happening is that when the user clicks on the element of a certain class, the code finds the id of that element, and then finds the related element and shows it. Then when the user clicks on the "close" button -- helpfully called `st134` by Illustrator -- it runs a function that closes the layer.

However, you're probably wondering: Where did `org-layer` come from? Well, I cheated. Illustrator didn't give the elements I wanted a class, so I added it myself. After Illustrator created the svg, I used Find-and-Replace to turn a layer -- like `<g id="Bergen_x5F_bergen_x5F_county_x5F_academies_x5F_org"> ` into `<g id="Bergen_x5F_bergen_x5F_county_x5F_academies_x5F_org" class="org-layer">`. 

## Option two: Grabbing it by element type
SVG and HTML are quite different, but they do have some overlap. For example, you can name them with IDs and classes, and manipulate them with CSS. This makes it a little easier when you need to, say, treat an SVG as if it's an HTML document.

In this case, I had non-showing text elements labeling sections of the pie chart with  `<path>` elements, like this:
```<path class="st6" d="M174.1,1615.7c-27.4,19.9-65.7,13.8-85.6-13.5c-4.5-6.2-7.9-13.3-9.8-20.7l37.8-9.7
				c3.1,11.9,15.2,19.1,27.1,16c2.7-0.7,5.3-1.9,7.6-3.5L174.1,1615.7z			">
<p>White: 31%</p>
			</path>
```
I needed to access them and show them when the user hovered over a section. So I put this within that `init()` function:
```
  $('path').hover(function(){
    var title = $(this)[0].innerHTML;
        $('<p class="tooltip"></p>')
        .append(title)
        .appendTo('body')
        .fadeIn('slow');
  }, function() {
      // Hover out code
      $(this).attr('title', $(this).data('tipText'));
      $('.tooltip').remove();
  }).mousemove(function(e) {
      var mousex = e.pageX + 20; //Get X coordinates
      var mousey = e.pageY + 10; //Get Y coordinates
      $('.tooltip')
      .css({ top: mousey, left: mousex })
  });
```
Let's break this down further. `$('path').hover(function()` means "when the user hovers on a SVG path in the document, run this code." Again, in this case, JQuery is fine treating SVGs just like an HTML document.

`var title = $(this)[0].innerHTML;` is a little more complicated. In this situation, I'm treating the SVG like a JSON, when the information I want is registered inside an array. the `innerHTML` takes the information pulled from that array and treats it like it's HTML that we can display, which is what's happening in the next line of code. 

The next couple lines are pretty boilerplate mouseover usage. I styled it with CSS.
```
.tooltip {
    display:none;
    position:absolute;
    border:1px solid #333;
    background-color:#161616;
    border-radius:5px;
    padding:10px;
    color:#fff;
    font-size:16px;
    font-family:Helvetica;
}
```

