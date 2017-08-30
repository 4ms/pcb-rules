## HOW TO MAKE A FACEPLATE in KiCAD##

  1. Illustrator
       * Open the Adobe Illustrator file of the artwork and hide all layers except the artwork (no holes, panel outline, milling, etc)

     * Save it ("Export for Screens...") as a PNG at 2000ppi, with no Anti-aliasing. This gives us 0.5mil resolution. 
     * Save it in a new folder called `MyProjectName-FACEPLATE` inside the kicad project directory. (replace `MyProjectName` with the name of the project we're working on, such as `DLD` or `SMR`...
     * Create a new folder inside `MyProjectName-FACEPLATE` called `artwork.pretty`
     * Optionally, create another new folder called `artwork sources` and put the Illustrator files and PNG exports (if you have them already) in there.

  2. Bitmap2component (KiCAD program)
     * Click "Load Bitmap" and select the PNG file
     * Set Format to Pcbnew (.kicad_mod file)
     * Set Options to Negative (sometimes you have click Normal and the Negative to get it to do Negative--it's a bug)
     * Set Threshold Value to 99
     * Set Board Layer for Outline: to Front silk screen
     * Verify the Black&White Picture preview looks accurate
     * Click "Export" and then  save the file in `MyProjectName-FACEPLATE/artwork.pretty`. Call it `MyProjectName-FACEPLATE-artwork-revXXX.kicad_mod`, where revXXX is the revision like "rev2c"
     * If the artwork is supposed to be on the copper layer (Gold or Silver), then unforutnately KiCAD does not support this directly. But it's easy to do manually:
     	* Export it into the Eco1.User layer instead of the Front silk layer
     	* Open the .kicad_mod in a text editor
     	* Do a global search and replace for `Eco1.User`, replacing it with `F.Cu`
     
  3. In KiCAD, open the pcb file (`MyProjectName.kicad_pcb`) and do a "Save As..." into the `MyProjectName-FACEPLATE` directory. Call the new file `MyProjectName-FACEPLATE.kicad_pcb`, and open it up in pcbnew

  4. Delete all traces, silk, and components from the file except the panel components (jacks, pots, etc..). Leave just the panel components and the board outline (Edge.Cuts layer)
 
  5. Use the Properties > Change footprint(s) dialog box to globally change each type of panel component (jack, button, LED, 9mm pot, 16mm pot, etc) to a footprint from the 4ms-faceplate library that starts with `FaceplateHole_` ****SAVE YOUR FILE FIRST****
     * Step-by-step instructions:
     * Click on a jack component.
     * Right-click and select `Properties` (or hit the shortcut key 'e')
     * Click the `Change footprint(s)` button
     * Select the second option: `Change footprints 'PJ301M-12'`. This will make your change apply to all instances of the PJ301M-12 footprint, not just the one you selected.
     * Click `View Footprints`
     * In the window that opens up, select the `4ms-faceplate` library and then select the `FaceplateHole_Jack_260` component. Double click the component to exit the window.
     * Notice in the `New footprint name (FPID)` text box, the text should have changed to read `4ms-faceplate:FaceplateHold_Jack_260`. (You also could have manually typed that it instead of selecting it from the View Footprints window)
     * Click `Apply`. It will ask you to confirm, click `Yes`
     * Repeat this process for every other type of panel component
![](img/faceplate_change_footprint.png)

  6. Create an outline in Edge.Cuts that matches the faceplate outline
     * In Illustrator, measure the X and Y distance from the center of a component on the pcb (eg: a jack hole) to a corner of the panel.
     * Also measure the Width and Height of the panel (height is always 5.059" for eurorack)
     * In Kicad, use the X/Y distances to figure out where the corner of the panel should be, relative to the center of that same component hole. Keep in mind the board is probably flipped from the view you see in Illustrator. Also keep in mind Illustrator and Kicad use different directions for + and -.
       * One way to do this, is to select everything in kicad and move it all so that the reference hole is at the origin (0,0).
       * Then draw a line for the bottom of the panel in the Edge.Cuts layer.
       * Edit line to enter in its X/Y values:
          * Set the starting X value to the X value you measured in Illustrator, and ditto for the Y value. Keep in mind that you might have to make a value negative or positive, depending on the direction you measured.
          * Add the Width and Height values to the starting X/Y values, and enter that as the ending X/Y values.
       * Duplicate this line, and immediatly do a "Cmd-M" (Move Exactly) command. Enter in the height of the panel as the amount to move the duplicated line (Y = -5.059). Pay attention to + or - direction (- is up in Kicad). This new line is now the top of the panel.
       * Repeat for the left side (draw it roughly, enter in the starting X/Y values, add the height to get the ending X/Y values)
       * Duplicate the left side and move by the panel width to get the right side
    
  
     * Delete the PCB outline, leaving just the faceplate outline and the `FaceplateHole_` components.
     
  7. Add rail-mounting slots: Add the `4ms-faceplate:FACEPLATE-Rail-mount-slot` components to the PCB and place where they need to be (center X:0.295, Y:0.118 from corners). Note that this footprint was altered using a text editor to make the lines and arcs be in the Edge.Cuts layer (which the pcb footprint editor doesn't allow for some reason)

     
  8. If the original PCB had the panel components on the Back layer, then now you have to __Flip all the components__ :
     * In kicad, select all, then hit "F" and the "R" twice. Then move it back to the origin
     * If you flipped, verify the Rail Mount holes are still correct-- for small boards with just two rail slots, they will be on the wrong side, so check that now. The A-100 mechanical specs are publised on doepfer's web site if you're ever unsure. We use slots so they are compatible with DIY and AS systems
    
     
  9. Add the artwork as a footprint:
     * Select Footprint Libraries Wizard from the Preferences menu, and add the artwork.pretty directory to "The current project only" (do not add it to global libraries).
 
     * An easy way is to click the Add Footprint icon, click "List All", then type "artwork" in the field. It'll only show for footprints with the name artwork, which is what you should have named the .kicad_mod file.
     * Center it, position it carefully to it's lined up with the holes.
     * Bug Alert: Sometimes bitmap2component doesn't make it Negative, even if you select it. The imported image should have a transparent background and the silk screen should be a colored foreground. If the Negative setting is wrong, then the imported image will be mostly solid color with the text and artwork being transparent. Go back and try importing again, clicking Negative/Normal back and forth to clear and reset it.
     * Verify it looks good! Look over all the text carefully. If there's anything weird, make changes in illustrator, save as PNG, and convert with bitmap2component again.
     
  10. Put all the holes onto the same GND net:
     * Sometimes you have to create the GND net first (sometimes it's already there?). Select from the menu bar: View -> List Nets. If "GND" is not listed, then create it as follows:
          * Save the pcb and close KiCAD.
          * Open the .kicad_pcb file in a text editor
          * At or around linee 9, you should see a line that says:   `(nets 0)`
          * Change this to read: `(nets 2)`
          * Then scroll down to around line 85 or so, until you see this line: `(net 0 "")`
          * Below it, add this line:   `(net 1 "GND")`
          * Save the file, close it, and re-open in KiCAD. Verify that View->List Nets shows a GND net

     * Click on each panel component, one at a time, and edit the Pad (not the footprint, we want to edit the pad itself).
     * It seems you have to have the component UnLocked. That way, when you click on it, it will ask you to edit the footprint or the pad. Select 'pad'. If the component is locked, it won't ask you this.
     * Change the Net of the pad to GND
     * Do this for all the jack and pot holes (or anything that must be grounded).
![Pad options box](img/faceplate_pad_options.png)

  11. Create the back layer copper zone:
     * Create a Zone on the B.Cu layer, with GND net assigned, and Clearance of 0. This creates a solid copper pour on the back side of the faceplate.
     * Verify the Zone connects to all the pads. If you skipped one when assigning them to GND, it won't connect to the copper pour, which could result in grounding/noise issues.

  12. Create an origin, using the "drill/place origin" tool. Place it on the bottom left Edge of the board (zoom in to get it exact within +/-0.001"). It should display as a red cross-hair
  
  
  13. Export the gerbers: 
    * F.Cu, B.Cu. F.SilkS, F.Mask, Edge.Cuts, and Drill file and Drill Map. Notice we skip the B.Mask here, we manually create it in the next step. Also, we do not have a B.Silk.
   
    * De-select `Plot footprint values`
    * De-select `Plot footprint references`
    * Select `Exclude PCB Edge layer from other layers`
    * Select `Use auxillary axis as origin`
    * Type `gbr/` for the `Output directory`.
    * Make sure `Plot Format` is set to `Gerber`
    * Click `Plot` and it will export the gerbers.
![Plot dialog box options](img/faceplate_plot_options.png)

    * Next click `Generate Drill File`
 		* Output directory: `gbr/`
    	* Drill Units: `Inches`
   		* Zeros Format: `Suppress leading zeroes` 
    	* Drill Map File Format is `Gerber`
   		* Drill File Options: check `Merge PTH and NPTH holes`, uncheck `Mirror y axis` and uncheck `Minimal header`
    	* Drill origin is `Auxillary Axis`
    	* Click `Drill File` and then click `Map File`
![Plot>>>Drill dialog box options](img/faceplate_plot_drill_options.png)

    	
14. In Finder or Terminal: duplicate the Back Copper file and rename it the Back Mask file:
    * Duplicate the B.Cu gerber file
    * Rename the duplated file as `MyProjectName-B.Mask.gbr`
    * (there shouldn't be an existing B.Mask.gbr file, but if there is, delete it)
 
15. Skip this step! (But in case you were wondering another way to create the gerbers manually, here's how:)
 	   * Copy a B.Mask.gbr and B.Cu.gbr file from a previous project. There are assorted sizes in the faceplate-masks repository. If the project has the same HP width then just copy it into the gbr/ directory and rename it `MyProjectName-FACEPLATE-B.Mask.gbr` and make another copy named `MyProjectName-FACEPLATE-B.Cu.gbr` 	   
 	   * If we don't have an existing B.Mask gerber for this HP size, then we can make it:
 	      	* Open the file in a text editor.
 			* Find the line that starts with `%ADD10R,`
 			* Change the next six digits to the new width. So if the line is `%ADD10R,1.23456X5.05900*%` then 1.23456 is the width of the old panel, and 5.05900 is the height. The height will be the same, so we only have to change the width. We should change that to 0.78700 for 4HP (for example), making it `%ADD10R,0.78590X5.05900*%`. **It is absolutely critical that the new number has the same number of digits as the old number (one digit before the decimal place and five digits after)**
 			* Next, find the line (two lines down usually) that starts with `X0`.
 			* Change the line to be the center of the board. So if it was `X0059055Y0252950D03*` then that says the center is 0.59055, 2.5295. The second value (2.5295) is half of the 5.0590 value from above and so it won't change. The first value will change: Divide the faceplate width by two (0.78700 / 2 = 0.39350) to get the first value. So now the line should read `X0039295Y0252950D03*`. **Again, it's critical that the number has the exact same number of digits as the previous value. Pad it with zeroes on the left if it doesn't.**
 			* Save the file as `MyProjectName-FACEPLATE-B.Mask.gbr` and make another copy named `MyProjectName-FACEPLATE-B.Cu.gbr`
 
 	
16. Create the gerbv project:
    * Go to terminal and `cd` into the MyProjectName-faceplate/gbr directory.
    * Type `kicadfp_gerbv MyProjectName-faceplate`
    * Or `gerbv_fpkicad MyprojectName-faceplate`
    * (if you don't have that installed, try the old command: `make_kicad MyProjectName-faceplate`)
		* This command looks for files named:
			* `MyProjectName-faceplate-F.Cu.gbr`
			* `MyProjectName-faceplate-B.Cu.gbr`
			* `MyProjectName-faceplate-F.SilkS.gbr`
			* `MyProjectName-faceplate-F.Mask.gbr`
			* `MyProjectName-faceplate-B.Mask.gbr`
			* `MyProjectName-faceplate-drl_map.gbr`
			* `MyProjectName-faceplate-Edge.Cuts.gbr`
			* `MyProjectName-faceplate.drl`
    		* If your files are not named exactly this (replacing `MyprojectName-faceplate` with whatever you typed above), then do a `ls` to see what the file names are. 
    		* Example: If your file names are SuperModule-rev1b-F.Cu.gbr, then you would type `make_kicad SuperModule-rev1b`
    * Note there is no Front Paste or Back Silk. So if you used the old command make_kicad, you'll get an error that F.Paste and B.SilkS are missing. This is OK, ignore the error.
    * Verify it looks good, and adjust as necessary. It's very common to have to make adjustments, so if you think it's perfect the first time, zoom in and check some more!
    * If you edited the B.Mask and B.Cu, check to make sure they looks good. They should line up with the board outline and just be one solid rectangle.

16. Note the dimensions and zip up the gerbers:
	* In Kicad, note the panel width and height
	* In the gbr/ folder, create an empty directory or a blank file with these dimensions in the name
	    * command line example: in terminal type `touch DIM_X-2.988_Y-5.059`
	    * Finder example: in Finder, create a new folder inside the gbr directory. Rename the folder `DIM_X-2.988_Y-5.059`. The folder should be empty.
	    * Zip up the gerber and drill files, and name the zip file `MyProject-revXX.zip`
	       * It's very important to put the revision number in the zip file name!
	* When we go to order this board, the PCB house will ask us to upload the zip file and enter in the dimensions. Having an empty folder or a file with the dimensions in the name makes it very easy to do both these things in one step!