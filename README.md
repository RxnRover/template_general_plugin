# Generic Plugin Template

This template provides the framework for a standalone control program and 
Rxn Rover plugin that enables basic control over an instrument, 
including setpoint control, start and stop commands, and monitoring of an 
example value (like temperature or pressure readings). The plugin keeps a log 
of all data collected in a CSV format for easy analysis using a spreadsheet 
program.

## Installation

### Prerequisites

This template, and any plugin created from this template, requires the 
[DynamicReentrant](https://github.com/RxnRover/DynamicReentrant) library. This
library is provided by the Rxn Rover team to facilitate communication with Rxn 
Rover. 

### Getting the Template

Download this template by clicking the "Code" button in the top right of its 
GitHub repository and selecting "Download ZIP". Extract the template and make 
a copy to use it. Copies of this template can be modified to create instrument 
plugins for Rxn Rover, using drivers created for each instrument.

### Integrating Template Into LabVIEW Project Creator

It is possible to integrate this plugin template into the "Templates" list of 
the LabVIEW project creator. The full instructions for project templates can be
found [here](https://knowledge.ni.com/KnowledgeArticleDetails?id=kA03q000000x1k8CAA&l=en-US). 
Abbreviated instructions are included below:

- Extract the ZIP file into your `<LabVIEW Data>/ProjectTemplates/Source` 
  directory, creating the `Source` directory if this is your first template.

- Copy the provided `general_plugin_template.xml` file into the 
  `<LabVIEW Data>/ProjectTemplates/MetaData` directory, creating the `MetaData`
  directory if needed.
  
- The General Plugin Template option should now be available in the LabVIEW 
  Project Creator dialog!
  
## Basic Usage

**NOTE:** It is strongly recommended that you have a functioning instrument 
driver which uses VISA resources before using this template. This will save a
lot of time developing plugins, since you will essentially just drop in 
the relevant driver calls and rename some indicators and labels. The instrument
plugin type must also be supported in Rxn Rover, or [support will need to be
added](https://rxnrover.github.io/RxnRover/dev_resources/tutorials/new_plugin_type/index.html).

Modification instructions are included as comments in the plugin code. 
A basic checklist will be included here to get you started with the plugin
template, since there is a lot of code, but not much that needs modification!

- Create a new project either by copying the `General Plugin Template`
  directory, or by selecting the `General Plugin Template` option in the 
  LabVIEW Project Creator (if the integration steps above were followed).
  
- Rename the project and the "Rename to match project" library to something 
  descriptive for the target instrument.
  
- Open `plugin.conf` in a text editor and update the relevant Type in Plugin 
  Details and Name and Type in Controller Details.
  
- Open `Main.vi` and edit the title; display units, following the directions
  on the front panel; numeric formatting of the fields; and default values in
  of the Front Panel controls.
  
- Open the Block Diagram for `Main.vi`, where you will have access to each 
  parallel loop of the plugin. If you are not changing the functionality of
  the plugin (write one setpoint, read one value, start, stop, etc.) you 
  probably only need to change the Instrument Manager Loop (IML), Logging 
  Message Loop (LML), and Acquisition Message Loop (AML).
  
- IML Changes
    - In the "Initialize" case, modify the instrument state typedef according
      to the instructions in the code. This typedef represents the state of
      the instrument, for example, a heater plugin may track the temperature
      setpoint, current temperature, whether it is currently heating, and
      any relevant error flags from the heater.
    - Each instrument state added in the previous bullet needs a corresponding 
      "Get State" message case in the IML, so the plugin knows how to query the
      instrument for the information. Create these message cases in the IML
      following instructions in the `--- Copy for Get State Messages ---` case 
      and the example cases below it in the case list.
    - Modify the "Start Instrument", "Stop Instrument", and "Change Setpoint" 
      cases for your instrument and drivers.
    - In the "Set VISA Resource" case, modify `Init.vi` "Connecting" case to
      properly verify the instrument connection and initialize the instrument.
      
- AML Changes
    - In the "Initialize" case, modify, add, and remove items from the 
      Acquisition Messages array. These messages will trigger the instrument
      state cases in the IML, so they must match the names of the IML cases.
      Make sure that "Report" is the last item in this array.
      
- LML Changes
    - Update the "Channels" array in the "Initialize" case to correspond to
      the instrument states listed in the Acquisition Messages array of 
      the AML in the previous step.


## Advanced Usage
  
### Reading and Plotting Multiple Data Values

Sometimes it is useful to plot multiple data values, either on the same plot or
on different plots. An example is plotting the current temperature, along with
the setpoint temperature to visualize the progress of a heater toward its 
target. This requires modification to multiple areas, along with the Basic 
Usage modifications above.

- In Main.vi, the Data Display Loop (DDL), DDL Data Type Def (beside the DDL), 
  and Update Plot Data.vi (in the DDL) functions must be modified to reflect
  the new data to be plotted.
- A new case must be created in the Instrument Manager Loop (IML) to query the 
  instrument for the desired information. Along with the new IML case, go to
  the Acquisition Message Loop "Initialize" case and add the name of the new 
  IML case to the "Acquisition Messages" array BEFORE the "Report" message.