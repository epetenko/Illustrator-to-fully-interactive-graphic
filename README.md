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
Did you add in those hidden layers to your Illustrator file? Great. Now here's what it did: it added a class to each of those hidden documents -- `st132` -- and in the CSS, added some style to it: `.st132{display:none;}`. If you've worked with CSS and JQuery before, you might be familiar with this next step.

I wanted to have the hidden layers show up when the user clicks on a certain section of the document. There are many ways to do this, but here's the one I used:
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
What's happening is that when the user clicks on the element of a certain class, the code finds the id of that element, and then finds the related element and shows it. Then when the user clicks on the "close" button -- helpfully called `st143` by Illustrator -- it runs a function that closes the layer.

However, you're probably wondering: Where did `org-layer` come from? Well, I cheated. Illustrator didn't give the elements I wanted a class, so I added it myself. After Illustrator created the svg, I used Find-and-Replace to turn a layer -- like `<g id="Bergen_x5F_bergen_x5F_county_x5F_academies_x5F_org"> ` into `<g id="Bergen_x5F_bergen_x5F_county_x5F_academies_x5F_org" class="org-layer">`. 

You don't necessarily have to do this. In fact, if there's just one or two elements that you need interactivity for, you can just reference them by ID:
```
  $('#click-on-this').on('click', function(){
    $('#hidden-layer').show();
```
The ID of the element is the name you gave that layer in Illustrator. [This tutorial](http://tomgermeau.com/2014/02/how-designers-can-create-interactive-prototypes-with-illustrator/) has another example of how you can do this.

## Option two: Grabbing it by element type
