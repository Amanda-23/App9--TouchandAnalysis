# App9--TouchandAnalysis
<!DOCTYPE html>
<html lang="en">

<!-- The following code has been developed by students and/or researchers of the Freshman Research Initiative, DIY Diagnostics Stream at The University of Texas at Austin.  This code is shared for demonstration purposes and should not be considered a product -- it is for entertainment purposes only.  Any user of this code does so at their own risk. Members of the DIY Stream, FRI, and The University of Texas system are not liable for anything related to this code.
 
  THIS CODE SHOULD NOT BE USED TO DIAGNOSE ANY KIND OF MEDICAL CONDITION.
 
  Further Information:
  http://cns.utexas.edu/fri
 
  Research Educator:
  Timothy E. Riedel
  triedel@utexas.edu
 
  Authors in chronological order of contribution:
  Author 1: Timothy E. Riedel
  Author 2: Amanda Yates
  
  References:
  http://docs.webplatform.org/wiki/concepts/programming/drawing_images_onto_canvas#Loading_the_image_programmatically
  http://www.html5rocks.com/en/tutorials/file/dndfiles/
  http://www.w3.org/TR/FileAPI/
  http://mobilehtml5.org/
  http://stackoverflow.com/questions/11929099/html5-canvas-drawimage-ratio-bug-ios
  
  Brief Description of Goal of Code:
  The goal is to create an app that is able to take a picture, display that on a canvas, and then display an image containing only blue pixels on an additional canvas. 
 
  Known Issues:
  None, so far
-->

 <head>
  <meta charset="utf-8">
  <title>Photo Transform</title>
  <meta name="viewport" content="width=device-width, initial-scale=.7">
  <meta name="apple-mobile-web-app-capable" content="yes">
  <link rel="stylesheet" href="https://code.jquery.com/mobile/1.3.2/jquery.mobile-1.3.2.min.css" />
  <script src="https://code.jquery.com/jquery-1.9.1.min.js"></script>
  <script src="https://code.jquery.com/mobile/1.3.2/jquery.mobile-1.3.2.min.js"></script>

 <script>
  var docMod = document.lastModified; // gets last modified date and time of the index.html file
  console.log("This file last modified  " + docMod); // displays last modified date and time in the javascipt browser console 
/* detectVerticalSquash & drawImageIOSFix = are functions that are critical due to a flaw in the iPhone camera software that squishes any photos displayed on an HTML5 canvas element. https://github.com/stomita/ios-imagefile-megapixel
*/
 function detectVerticalSquash(img) {
  var iw = img.naturalWidth, ih = img.naturalHeight;
  var canvas = document.createElement('canvas');/* creates first canvas variable */
  canvas.width = 1;
  canvas.height = ih;
  var ctx = canvas.getContext('2d');
  ctx.drawImage(img, 0, 0);
  var data = ctx.getImageData(0, 0, 1, ih).data;
  var sy = 0;
  var ey = ih;
  var py = ih;
  while (py > sy) {
   var alpha = data[(py - 1) * 4 + 3];
   if (alpha === 0) {
    ey = py;
   } 
   else {
    sy = py;
   }
   py = (ey + sy) >> 1;
  }
  var ratio = (py / ih);
  return (ratio===0)?1:ratio;
 }
 function drawImageIOSFix(ctx, img, sx, sy, sw, sh, dx, dy, dw, dh) {
  var vertSquashRatio = detectVerticalSquash(img);
  ctx.drawImage(img, sx, sy, sw, sh, dx, dy, dw, dh / vertSquashRatio);
 }
/* window.onload necessary to keep javascripts from running before the app gets a chance to load entirely. */
 window.onload = function() {
  var fileInput = document.getElementById('fileInput');
  var messageDisplayArea = document.getElementById('messageDisplayArea');
  /* fileInput.addEventListener looks for any change in the <input> tag indicating that the user has selected a file (picture). */
  fileInput.addEventListener('change', function(e) {
    var file = fileInput.files[0];
    var imageType = /image.*/;
    /* if (file.type.match(imageType)) checks that the selected file is indeed an image. */
    if (file.type.match(imageType)) {
      /* new FileReader(); and reader.onload start some kind of file reading process in javaScript. I do not understand why this is necessary although it may simply slow things down to let the image load. */
      var reader = new FileReader();
      reader.onload = function(e) {
        /* messageDisplayArea.innerHTML not needed but allows you to send a message at this point in the program. */
        messageDisplayArea.innerHTML = "You picked an image!";
        /* new Image() and img.onload start some kind of new image creation process in javaScript. I do not understand why this is necessary although it may simply slow things down to let the image load. */
        var img = new Image();
        img.onload = function() {
          /*This finally draws the image onto the first drawing canvas. The specific canvas is referred to by "context". This is established down in the HTML part of the file just under the first <canvas> tag. If we weren't worried about fixing an iPhone bug we would use context.drawImage(image, 0, 0, img.naturalWidth, img.naturalHeight, 0, 0, 600, 500) instead of drawImageIOSFix */
        
          drawImageIOSFix(context,img, 0, 0, img.naturalWidth, img.naturalHeight, 0, 0, 600, 500);
          /*  +++++++++++++++ BEGIN IMAGE PROCESSING +++++++++++++++ */
          /*  ++++++++++++++++++++++++++++++++++++++++++++++++++++++ */
          /* PUT ANY CHANGES TO THE JAVASCRIPT HERE  */
          // func = 'color'; // last used function 
            drawImageIOSFix(context2,img, 0, 0, img.naturalWidth, img.naturalHeight, 0, 0, 600, 500);//this will appear in second canvas
            var imgd = context2.getImageData(0, 0, 600, 500);//getImageData refers to the image in first canvas
            var data = imgd.data;
            for (var i = 0, n = data.length; i < n; i += 4) {
            data[i] = 0; // red color pixels
            data[i+1] = 0; // green color pixels
            } //end of color intensity function to leave blue pixels remaining
             context2.putImageData(imgd, 0, 0);// put image data back to context2 (on second canvas)
      } // end of img.onload
        
        img.src = reader.result;
      } //  end of reader.onload
      
      reader.readAsDataURL(file);
    } // end of the if statement checking that file is an image
    else
    {
      // this is what happens if the orignial file selected is not an image file
      messageDisplayArea.innerHTML = "File not supported!"
    } // end of the else statement if file in NOT an image
  }); // end of the fileInput.addEventListener function
 } // end of the window.onload function
 </script>
 </head>

 <body>
  <div data-role="page">
    <div data-role="header">
    <h1>Primary Colors</h1></div><!-- /header -->
    <div data-role="content">	
      <h2>Push button to take picture.</h2>
      <div>
        Press blue button to create or choose image file:
        <input type="file" id="fileInput" STYLE="background-color: #001AFF;"><!-- the color can be changed using RGB color codes-->
      </div>
      <div id="messageDisplayArea"></div>
      
      <canvas id="myCanvas" width=600 height=500 style="border:1px solid #d3d3d3;"></canvas><!-- creates first canvas-->
      <script> /* This script initiates the cavas and puts the text on the canvas "myCanvas" */
        var canvas = document.getElementById('myCanvas');/* variable for first canvas--following code will appear in the first canvas*/
        var context = canvas.getContext("2d");
        context.font = '20pt Calibri';/*font type and size*/
        context.fillStyle = "black";/*text color*/
        context.fillText("Original image will go here", 10, 20);/*this text will appear in first canvas*/
      </script><!-- closes script for the first canvas-->
     <canvas id="canvas2" width=600 height=500 style="border:1px solid #d3d3d3;"></canvas><!-- creates second canvas element-->
     <script>/* this script initiates the second canvas and puts the text on "canvas2"*/
        var canvas2 = document.getElementById("canvas2");/* variable that allows second canvas to be called for*/
        var context2 = canvas2.getContext("2d");//must have context2 and canvas2 to ensure the following text will appear in the second canvas
        context2.font = '20pt Calibri';/*font type and size--context2 ensures that it will appear in canvas2*/
        context2.fillStyle = "black";/*text color*/
        context2.fillText("Transformed image will go here", 10, 20);/*this text will appear in second canvas*/
     </script><!--closes the script for the second canvas-->
    </div><!-- /content -->
    <div data-role="footer" data-position="fixed">
      <p>DIY Diagnostics</p>
    </div><!-- /footer -->
  </div><!-- /page -->
 </body>
</html>
