## Simple output module
After being familiar with DEMO and running, we need to plan for practical applications. Here we take a 24V 16-channel NPN output module as an example (optocoupler or ULN2803 output).

If you copy the DEMO project directly, you need to modify the .project file under the project folder, modify the name tag and the entire folder name and then import it from the dave software.

## ESC section
1. Use excel to edit /SSC/XMC_ESC.xlsx, modify DATA (remove INPUT, and keep a 0x01 UINT OUT_GENERIC as output.

2. Use SSC TOOLS to edit /SSC/XMC_ESC.esp, modify Vendor id, Product Code, Device Name and other information

3. TOOL->Application->Import select the xlsx file that has just been modified

4. Project->Create New Slave Files, specify the directory and ESI file name, click Start to generate the Slave program and device description file

5. Unzip Patch.zip to the current directory and overwrite it.

## ESC modification
Since this slave only has output, you need to modify the slave application.

1. Edit /SSC/src/XMC_ESC.c to shield 226 lines (because there is no IN_GENERIC, so there is no TxPDO, the InputSize is 0.

2. Shield the content of APPL_InputMapping in line 270 for the same reason as above.

3. (Optional) If it is 32-bit and above data, you need to modify the UINT16 * of OutputMapping to UINT32 *, and modify the calling content of ecatappl.c at the same time.

4. Modify process_app in lines 290 and 293 to remove TOBJ6000 *IN_GENERIC for the same reason as above.

## Main program modification
1. Initialize the relevant pins in Init_GPIO. I also added .output_Strength=XMC_GPIO_OUTPUT_STRENGTH_WEAK. The weak drive capability seems to be helpful for heat dissipation.

2. Modify the definition of process_app and remove IN_GENERIC.

3. Associate the pins in Process_app, such as: digitalWrite( P2_5 ,(OUT_GENERIC->OUT >> 2) & outenable);

(Shift OUT_GENERIC.OUT by 2, that is, get bit2 as the last bit, and sum the gate with outenable (0x0 or 0x1). Editing the mapping mapping can be pulled out in EXCEL, which is more convenient)

## Debug and download
If you have programmed other Vendor id or product id programs before, please use jlink commander to erase them manually. These contents are stored in EEPROM and may not be erased completely when downloading.
