#SVG2Canvas Converter

[Online demo - http://demo.qunee.com/svg2canvas/](http://demo.qunee.com/svg2canvas/)

#使用方式

##Directory Structure

```
src/
  --canvg-master/ -- canvg.js
  canvg2.js -- 修改生成的canvg.js
  insert.code.js -- 增加的扩展代码
  run.js -- node.js代码，使用 node run运行，生成canvg2.js
  run.sh -- 运行，生成canvg2.js
index.html -- SVG to Canvas 工具
```

##Run

Please run run.js or run.sh to generate the modified src/canvg2.js
```
cd src
node run
```
Then visit index.html in the browser.
![SVG to HTML5 Canvas](image/Screen-Shot-2015-04-30-at-6.16.41-PM-1024x581.png)

#用途说明

[http://blog.qunee.com/2015/04/svg-to-canvas在线转换工具/](http://blog.qunee.com/2015/04/svg-to-canvas在线转换工具/)

There are two kinds of graphics solutions in HTML5: SVG and Canvas, both of which have their own characteristics. Not to mention here, we often use SVG as a node icon in Qunee graphics components, which performs well in most scenarios, but under IE Some minor problems, IE can draw SVG images to Canvas, but can not read SVG pixel information, which means that some special features (tracking image edges, image dyeing, etc.) can not be achieved, then we consider choosing SVG Parse, convert to Canvas drawing code, so you can use Canvas to draw SVG images

![SVG to HTML5 Canvas](http://blog.qunee.com/wp-content/uploads/2015/04/Screen-Shot-2015-04-30-at-6.48.15-PM-1024x567.png)

## canvg.js

Yes, someone is doing something like this. [Canvg.js](https://github.com/gabelerner/canvg) is a tool library for converting SVG to Canvas. Even some SVG animation effects can be achieved, but there are many problems with canvg.js:

### Inevitable SVG compatibility issues

SVG is a very complex vector graphics language. Converting to Canvas is equivalent to writing a standalone SVG viewer. Even Adobe's SVG Viewer will have compatibility issues, not to mention a small js tool library.

### Canvg.js cannot generate Canvas drawing code

Canvg.js can actually draw SVG onto Canvas, but can't generate drawing code, which means that you need to introduce this class library and dynamically parse SVG. There is extra performance overhead, plus canvg.js itself is not perfect, code Encapsulation or functionality is flawed, and introduction in the production environment will cause problems.

### Professor Cloud

Search online svg to canvas, you can find this: [ProfessorCloud Svg-to-Canvas](http://www.professorcloud.com/svg-to-canvas/) can convert SVG into Canvas, and output javascript code, in line with the need to make vector icons, Qunee early built-in icon This is all done, but this tool is not perfect, does not support gradients, does not support textures, most SVG icons can not be converted, so we consider writing an online tool for the convenience of customers, we checked [ProfessorCloud Svg-to-Canvas](http://www.professorcloud.com/svg-to-canvas/) code, it is modified on the basis of an old version of canvg.js, is to write dead code, inconvenient to upgrade and maintain later, we think of a more Good way to achieve

## SVG2Canvas

We think of a better way to convert SVG to Canvas code, online address: [http://demo.qunee.com/svg2canvas/](http://demo.qunee.com/svg2canvas/)

### Implementation Ideas

By rewriting the HTMLCanvasElement related methods, the basic idea is to rewrite the related drawing and setting code in CanvasRenderingContext2D.prototype, and add the function of producing js code, such as the processing of CanvasRenderingContext2D#fill() method, we may want Rewrite this method
<pre class="prettyprint">CanvasRenderingContext2D.prototype.fill2 = function(){
   this.fill();
   appendCode('ctx.fill();\n'); // Generate js code
}</pre>
Of course, it is actually more complicated than this. Need to consider how to rewrite the method? How to convert a parameter object into a string? In addition, for complex SVG, you may need to create a temporary Canvas object, also involves some regular expressions, but also pay attention to the scope problem, spent a little effort, and implemented a good set of tools, basically canvg.js can Supported svg, can be very good conversion, support Gradient, support Pattern, etc.

### Online Service

If a good thing is just used by itself, it is too wasteful, so we spent a lot of time to make a good interface and publish it.

Online address：[http://demo.qunee.com/svg2canvas/](http://demo.qunee.com/svg2canvas/)

Support Chrome, Safari browser, left and right layout distribution, drag and drop the svg file to the left dotted box, you can generate the corresponding image registration code in Qunee for HTML5, and preview the image in Qunee for HTML5. The effect is as follows:

![SVG to Canvas Code Converter](http://blog.qunee.com/wp-content/uploads/2015/04/Screen-Shot-2015-04-30-at-6.16.41-PM-1024x581.png)

### Use the generated code

Save the code in the right text box to the js file (such as SVGIcons.js), and introduce the js in the HTML, then you can directly use the svg file name as the node icon.
<pre class="prettyprint">node.image = 'DataCenter.svg';
</pre>
Sample code:
<pre class="prettyprint">&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
&lt;title&gt;Hello Qunee for HTML5&lt;/title&gt;
&lt;meta charset="utf-8"&gt;
&lt;/head&gt;
&lt;body style="margin: 0px"&gt;
&lt;div style="position: absolute; width: 100%; top: 0px; bottom: 0px;" id="canvas"&gt;&lt;/div&gt;
&lt;script src="http://demo.qunee.com/lib/qunee-min.js"&gt;&lt;/script&gt;
&lt;script src="SVGIcons.js"&gt;&lt;/script&gt;
&lt;script&gt;
var graph = new Q.Graph('canvas');

var node = graph.createNode("SVG");
node.image = 'DataCenter.svg';

&lt;/script&gt;
&lt;/body&gt;
&lt;/html&gt;
</pre>
Running Result:

![hello svg](http://blog.qunee.com/wp-content/uploads/2015/04/Screen-Shot-2015-04-30-at-5.35.31-PM.png)