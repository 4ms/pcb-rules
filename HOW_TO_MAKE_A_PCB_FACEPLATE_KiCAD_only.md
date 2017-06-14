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
 
  5. For every panel component, add a footprint from the 4ms-faceplate library that starts with `FaceplateHole_`
     * For instance: For each jack, add a `FaceplateHole_Jack_260` component.
     * Line up the `FaceplateHole` components with the center of the panel component (jack, pot, etc). Copy/paste in the exact XY coordinates by selecting the components and pressing "e" (do not use the cursor's XY, it's not precise) 
      * For any slots or square/rectangular hole such as sliders, draw lines in the Edge.Cuts layer (or there may be a KiCad component already for it)
     * Verify eveything is lined up, and then delete all panel components.

  6. Create an outline in Edge.Cuts that matches the faceplate outline
     * You probably need to check in Illustrator what the offset is from the pcb and the panel.
  
     * Delete the PCB outline, leaving just the faceplate outline and the `FaceplateHole_` components.
     * Add rail-mounting slots: Add the `4ms-faceplate:FACEPLATE-Rail-mount-slot` components to the PCB and place where they need to be (center X:0.295, Y:0.118 from corners). Note that this footprint was altered using a text editor to make the lines and arcs be in the Edge.Cuts layer (which the pcb footprint editor doesn't allow for some reason)

     
  7. If the original PCB had the panel components on the Back layer, then now you have to __Flip all the components__ :
     * In kicad, select all, then hit "F" and the "R" twice. Then move it back to the origin
     * If you flipped, verify the Rail Mount holes are still correct-- for small boards with just two rail slots, they will be on the wrong side, so check that now.
    
     
  8. Add the artwork as a footprint:
     * Select Footprint Libraries Wizard from the Preferences menu, and add the artwork.pretty directory to "The current project only" (do not add it to global libraries).
     * You might have to close the project and re-open it to get KiCAD to see the new library (sometimes you don't have to???)
 
     * An easy way is to click the Add Footprint icon, click "List All", then type "artwork" in the field. It'll only show for footprints with the name artwork, which is what you should have named the .kicad_mod file.
     * Center it, position it carefully to it's lined up with the holes.
     * Bug Alert: Sometimes bitmap2component doesn't make it Negative, even if you select it. The imported image should have a transparent background and the silk screen should be a colored foreground. If the Negative setting is wrong, then the imported image will be mostly solid color with the text and artwork being transparent. Go back and try importing again, clicking Negative/Normal back and forth to clear and reset it.
     * Verify it looks good! Look over all the text carefully. If there's anything weird, make changes in illustrator, save as PNG, and convert with bitmap2component again.
     
  9. Skip this step! (It was: Create a Zone on the B.Cu layer, with \<no net\> assigned, and Clearance of 0. This creates a solid copper pour on the back side of the faceplate.... But we don't do this because it outputs an incomplete rectangle with gaps between holes, so the faceplate will offer no grounding. To fix it, we might be able to create a dummy netlist that puts all the holes into a GND net, then create a copper pour assigned to GND...)

  10. Create an origin, using the tool. Place it on the bottom left Edge of the board (zoom in to get it exact within +/-0.001"). 
  
  11. Export the gerbers: F.Cu, F.SilkS, F.Mask, Edge.Cuts, and Drill file and Drill Map. Notice these are all front layer, you make the back layer files manually
    * De-select Plot footprint values and references
    * Select Exclude PCB Edge layer from other layers
    * Select "use auxillary axis as origin"
    * Type `gbr/` for the "Output directory".
    * Make sure Plot Format is set to `Gerber`
    * Click "Plot" and it will export the gerbers.
    * Next click "Generate Drill File"
 		* Output directory: `gbr/`
    	* Drill Units: `Inches`
   		* Zeros Format: `Suppress leading zeroes` 
    	* Drill Map File Format is `Gerber`
   		* Drill File Options: check `Merge PTH and NPTH holes`, uncheck `Mirror y axis` and uncheck `Minimal header`
    	* Drill origin is `Auxillary Axis`
    	* Click `Drill File` and then click `Map File`
  
  12. Make a Back Mask and Back Copper file:
 	* Copy a B.Mask.gbr and B.Cu.gbr file from a previous project. There are assorted sizes in the faceplate-masks repository. If the project has the same HP width then just copy it into the gbr/ directory and rename it `MyProjectName-FACEPLATE-B.Mask.gbr` and make another copy named `MyProjectName-FACEPLATE-B.Cu.gbr`
 	* If we don't have an existing B.Mask gerber for this HP size, then we can make it:
 		* Open the file in a text editor.
 		* Find the line that starts with `%ADD10R,`
 		* Change the next six digits to the new width. So if the line is `%ADD10R,1.23456X5.05900*%` then 1.23456 is the width of the old panel, and 5.05900 is the height. The height will be the same, so we only have to change the width. We should change that to 0.78700 for 4HP (for example), making it `%ADD10R,0.78590X5.05900*%`. **It is absolutely critical that the new number has the same number of digits as the old number (one digit before the decimal place and five digits after)**
 		* Next, find the line (two lines down usually) that starts with `X0`.
 		* Change the line to be the center of the board. So if it was `X0059055Y0252950D03*` then that says the center is 0.59055, 2.5295. The second value (2.5295) is half of the 5.0590 value from above and so it won't change. The first value will change: Divide the faceplate width by two (0.78700 / 2 = 0.39350) to get the first value. So now the line should read `X0039295Y0252950D03*`. **Again, it's critical that the number has the exact same number of digits as the previous value. Pad it with zeroes on the left if it doesn't.**
 		* Save the file as `MyProjectName-FACEPLATE-B.Mask.gbr` and make another copy named `MyProjectName-FACEPLATE-B.Cu.gbr`
 
 	
  13. Create the gerbv project:
    * Go to terminal and `cd` into the MyProjectName-faceplate/gbr directory.
    * Type `make_kicad MyProjectName-faceplate`
		* This command looks for files named:
			* `MyProjectName-faceplate-F.Cu.gbr`
			* `MyProjectName-faceplate-B.Cu.gbr`
			* `MyProjectName-faceplate-F.SilkS.gbr`
			* `MyProjectName-faceplate-B.SilkS.gbr` (not used in faceplates)
			* `MyProjectName-faceplate-F.Mask.gbr`
			* `MyProjectName-faceplate-B.Mask.gbr`
			* `MyProjectName-faceplate-F.Paste.gbr` (not used in faceplates)
			* `MyProjectName-faceplate-drl_map.gbr`
			* `MyProjectName-faceplate-Edge.Cuts.gbr`
			* `MyProjectName-faceplate.drl`
    		* If your files are not named exactly this (replacing `MyprojectName-faceplate` with whatever you typed above), then do a `ls` to see what the file names are. 
    		* Example: If your file names are SuperModule-rev1b-F.Cu.gbr, then you would type `make_kicad SuperModule-rev1b`
    * Note there is no Front Paste or Back Silk. So you'll get an error that F.Paste and B.SilkS are missing. This is OK, ignore the error.
    * Verify it looks good, and adjust as necessary. It's very common to have to make adjustments, so if you think it's perfect the first time, zoom in and check some more!
    * If you editted the B.Mask and B.Cu, check to make sure they looks good. They should line up with the board outline and just be one solid rectangle.