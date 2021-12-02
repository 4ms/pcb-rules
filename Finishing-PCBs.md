##Finishing a PCB for the board house##

Pre-checks:

1. Create an origin on one of the corners: Use the "place and drill origin" tool (not one of the other many origin tools!)
2. Push your commit for review of teammates
3. When reviewed and confirmed OK, push your commit (if any changes)

4. Export the gerbers: Select "Plot" from the File menu

  - `F.Cu`
  - `F.SilkS`
  - `F.Mask`
  - `B.Cu`
  - `B.SilkS`
  - `B.Mask`
  - `F.Paste` (only if it's an SMT PCBA project)
  - `B.Paste` (only if it's an SMT PCBA project AND there are components on the back)
  - `Edge.Cuts`
  
    * Check Plot footprint values
    * Check Plot footprint references
    * Check Exclude PCB Edge layer from other layers
    * Check Use auxillary axis as origin
    * Nothing else is checked.
    * Format is "4.6"
    * Type `gbr/` for the "Output directory".
    * Make sure Plot Format is set to `Gerber`
    * Click "Plot" and it will export the gerbers.
    
5. Export the Drill files: From the Plot window:
    * Next click "Generate Drill File"
 		* Output directory: `gbr/`
 		* File Format "Excellon"
    	* Drill Units: `Inches`
   		* Zeros Format: `Suppress leading zeroes` 
    	* Drill Map File Format is `Gerber`
   		* Drill File Options: check `Merge PTH and NPTH holes`, uncheck `Mirror y axis` and uncheck `Minimal header`
    	* Drill origin is `Auxillary Axis`
    	* Click `Drill File`
		* Click `Map File`
		* Close the windows

6. Verify the design with Gerber Viewer:
  * Open up Gerber Viewer from the Kicad project window.
  * Select File > Open Gerb Job file... and open the .gbrjob file that's was just created in the gbr/ folder.
  * BEFORE CLICKING ANYWHERE: Select File > Open Excellon Drill File and open the .drl file in the gbr/ folder.
       - If you did click somewhere, then before you open the .drl file, make sure that in the Layers Manager pane on the right, the first empty layer is selected (a little blue arrow shows which layer is selected, and empty layers are named "Graphic Layer ##")
  * Verify the layers look good: zoom in and out, hide/show layers, select layers to bring them to the front, etc.
   		- TODO: list specific things to look for (problems, etc)
 
  * Zip up the gerber files and drl files into a zip file named `MyProject-revision#.zip`. revision# should be either `p#` such as p1, p2, p3, etc for prototypes, or `v#.#` such as `v1.2` for production boards.
  * Send the files to JLCPCB or Imagineering and you're done! 

	
7. Create a tag in git that tells us the PCB version and that we ordered boards. For example: "p3 ordered with PCBCart". Or "v1.1 ordered PCBA with ___". Remember to push the tag (or else no one else will be able to see it)