##Finishing a PCB for the board house##

Pre-checks:

1. Create an origin on one of the corners: Use the "place and drill origin" tool (not one of the other many origin tools!)
2. Push your commit for review of teammates
3. When reviewed and confirmed OK, push your commit (if any changes)

4. Export the gerbers: Select "Plot" from the File menu

  - `F.Cu (aka Top)`
  - `F.SilkS`
  - `F.Mask`
  - `B.Cu` `(aka Bottom)`
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

5. Verify the design with gerbv:
	To do this, you need gerbv installed, and also Dan's script called make_kicad
	   * In terminal, navigate to the gbr/ directory
	   * type: `gerbv_kicad PROJECT_NAME` where PROJECT_NAME is the beginning of the gerber files up until the last dash '-'. Example "qcd-expander-20-F.SilkS.gbr" project name is "qcd-expander-20"
	   * When it's good, zip up all the gbr and drill files into a zip file that's the name of the project and revision:
	   `QCDEXP-kit-p1` or `-v2.1`

5. Create a tag in git that tells us the PCB version and that we ordered boards. For example: "p3 ordered with PCBCart". Or "v1.1 ordered PCBA with ___". Remember to push the tag (or else no one else will be able to see it)