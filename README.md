# Illustrator-to-fully-interactive-graphic

Ever done this? "I love working with Illustrator...but I need an interactive file!"

Fear not! There is a way to make an Illustrator file interactive. It's not an everyday route I'd recommend, but if you're in a pickle, you can use this trick to add an interactive element:

1. Create hidden layers in your Illustrator file.
2. "Save As" an svg. This will turn your document into a code-based document.
3. Create a webpage that will load the svg, using JQuery. 
4. Either using the name of the layer — which is the id of that section of the SVG — or using a class that you've added, use JQuery to trigger an event by click or hover. 
