# Fusion2Pybullet

Developed from [@syuntoku14/fusion2urdf](https://github.com/syuntoku14/fusion2urdf). 

### What is this script?

A Fusion 360 script to export URDF files. This is a PyBullet adpative version. Compared to [@yanshil/Fusion2PyBullet](https://github.com/yanshil/Fusion2PyBullet), this version supports both "Capture History" and "Direct Design", does not create extra geometry in the Fusion model, and does not require flattening the model tree.

This exports:

* .urdf files of the model
* .stl files of your model
* A example hello.py to load your model into PyBullet.

Note: This script only supports "Revolute", "Rigid" and "Slider" joints currently. 

Improvements I'd like to make (eventually): 

* Fix the stl naming conventions (currently appends all nested link names together)
* Support for linked components 
* Support for "backwards" Joints (it shouldn't matter the order in which the user selects components)

---

### Fusion Add-in
Add this script into Fusion 360 via Tools -> Add-Ins

![](./imgs/1_plugin.png)

#### Before using this script

1. Some other notes for getting avoid of warnings: 
   1. Change language preference to English
   2. Rename any full-width symbol to half-width symbol (like `。` and `（）`)
2. Rename the root component `base_link`
3. Suggestion: Use [**Joint2Graphviz**](https://github.com/yanshil/Joint2Graphviz) to check your assembled structure! 
4. **Check if your default unit is mm or not. If you set it to some other unit, you need to manually adjust the scale when exporting the stl fils. [See FAQ](#faq)** 

#### Using script inside Fusion 360: Example

1. Set up the components properly

- [x] Unit should be mm (See [FAQ](#what-if-my-unit-is-not-mm) if it's not)

- [x] A base_link

- [x] Your file should not conatin extra file links. If any, right click on the component, do 'Break Link'
	- [x] File links will be generated when you do something like 'Insert into current design'
	- [x] So please make sure you made a backup before you do the 'Break link' operations.

- [x] Check component and joint names (Set English as the language if necessary)

- [x] **IMPORTANT! Set up joints properly** 

	* Supplementary script: **Joint2Graphviz** will generate a txt file capable for Graphviz. Copy the content to [WebGraphviz](http://www.webgraphviz.com/) to check the graph. Usually a correct model should be a DAG with 'base_link' as the only root.
	
	* In fusion, when you hit 'J' to assemble joints, note that the exporter consider **component 1 as 'child' and component 2 as 'parent'**. For example, when you want to assemble a 4-wheel car with middle cuboid as `base_link`, you should assemble the vehicle with wheel as component 1 and 'base_link' as component 2.

	* For example, you should be assemble your model to make result of `check_urdf simple_car.urdf`  like the following. i.e. BL, BR, FL, FR as component 1 and base_link as component 2 when you assemble these 4 joints.
	```
    robot name is: simple_car
	  ---------- Successfully Parsed XML ---------------
	  root Link: base_link has 4 child(ren)
	      child(1):  BL_1
	      child(2):  BR_1
	      child(3):  FL_1
	      child(4):  FR_1
	```

2. Run the script and select storing location

   * ![](./imgs/5_files.png)
   
3. Enjoy from `python hello_bullet.py` !

## FAQ

###  What to do when error pops out

* Bugs are usually  caused by wrongly set up joints relationships
* Nest-component support might also lead to undocumented bugs. So remove the nesting structure helps a lot.

Since the script still cannot showing warnings and errors elegantly, if you cannot figure out what went wrong with the model while bugs are usually  caused by wrongly set up joints relationships, you can do the following things:

1. Make sure every joints are set up correct (parent and child relationship). If failed ---> 
2. Re-tidy your design to make it not include any nest-components. Use this script. If failed --->  
3. Try the stable version [Branch: stable](https://github.com/yanshil/Fusion2Pyblluet/tree/stable).
4. Run with debug mode and check what happens ![](./imgs/6_debug.PNG)

### How to check if I assembled my links and joints correctly

A supporting script here: [Joint2Graphviz](https://github.com/yanshil/Joint2Graphviz) for details

### What if my unit is not mm

You have to modify `Bullet_URDF_Exporter/core/Link.py`. Search `scale`

