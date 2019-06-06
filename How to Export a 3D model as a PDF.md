# How to Export a 3D model as a PDF

1) In KiCad, copy the PCB file to a new one and put it in a folder called "3d" inside the project folder

2) Open up this PCB file and remove the 3D model for every component you don't want visible. For sending to a PCBA house, we don't want to show them the parts we'll be adding. (unfortunately, you can't just make then "Virtual" you actually have to remove the link to the model for each and every part....)

3) Preview the 3D model in kicad's built-in 3d viewer. Make sure every component type is set to visible in the 3d viewer's preferences (TH/SMT/Virtual)

4) When it looks good, export the VRML file from KiCad. File > Export > VRML... and call it something .wrl

5) Open Meshlab and select File > Import Mesh... and select the .wrl file.

6) Do File > Export Mesh as..., pick "U3D format" and name the new file something .u3d (remove the .wrl extension)

**Note: You cannot have more than one dot "." in the name or else pdflatex chokes! Listen-Four-v1.2.wrl.u3d does not work, it has to be Listen-Four-v1-2.u3d**

7) On the next screen make sure to check the "color" checkbox.
Click Save. It might take 30 seconds or more

8) You might see a .tex file created with the .u3d file. If not, use this (Change NAME-OF-THE-u3d-FILE.u3d to the actual file name):


```
\documentclass[a4paper]{article}
\usepackage[3D]{movie15}
\usepackage{hyperref}
\usepackage[UKenglish]{babel}
\begin{document}
\includemovie[
	poster,
	toolbar, %same as `controls'
	label=NAME-OF-THE-u3d-FILE.u3d,
	text=(NAME-OF-THE-u3d-FILE.u3d),
	3Daac=60.000000, 3Droll=0.000000, 3Dc2c=-0.984000 -4.943800 2.184000, 3Droo=5.493566, 3Dcoo=-0.983992 0.130775 -2.183500,
	3Dlights=CAD,
]{\linewidth}{\linewidth}{NAME-OF-THE-u3d-FILE.u3d}
\end{document}

```
To change the zoom, adjust the 3Droo parameter (larger number is more zoomed out. Try 200.0). 

3Dc2c is the rotation of the object.

3Dcoo seems to be the offset X Y(Left/Right) Z and also zoom?

9) For your first time, you have to install pdflatex and movie15

  * A summary of the instructions from [here](https://tex.stackexchange.com/questions/307483/setting-up-basictex-homebrew) 
  * Install basictex with brew: `brew cask install basictex`
  * Look for where it installed the .pkg file: 
 `open /usr/local/Caskroom/basictex/` should open a window and open up the folder (maybe called 20160523) and inside should be a file called mactex-basictex-20160523.pkg. Double-click this file to install it.
  * Close the terminal window and open a new one

10) Install the movie15 tex package:

  * Download from here: [ctan](https://ctan.org/tex-archive/macros/latex/contrib/movie15/?lang=en). Or direct download of the zip [here](http://mirrors.ctan.org/macros/latex/contrib/movie15.zip)
  * Find the movie15.sty file, and put it in a folder called:
  `~/Library/texmf/tex/latex/local/` 
  (you probably have to create those folders)
  
11) In the terminal, make sure the .tex file and the .u3d file are in the same folder. Run `pdflatex TEX-NAME.tex`. 

It should create a .pdf that can be opened in Adobe Reader 7 or higher. It cannot be opened in the mac/apple Preview program.