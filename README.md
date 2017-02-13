<link rel='stylesheet' href='css/swiss.css'/>

# Roomle Configurator Script Documentation

## Table of Contents
1. [Getting started](#gettingStarted)
2. [Roomle Configuration Format](#configuratorFormat)
3. [Component Definition](#componentDefinition)

## <a name="gettingStarted"></a>Getting started

### About the Roomle Configurator Script

Roomle Configurator Script is used to describe Components and Configurations which can be interpreted by the Roomle Configurator. Components are definitions how a product should be displayed and contains the geometry and logic to accomplish that.

### How to script Roomle Components
The easiest way to learn scripting for the Roomle Configurator is to see a simple example. The following code snippet is a script representing a table shown on the image.

![The example table component](figures/table.png)

#### The component definition
We start with one table consisting of a table top and four legs. The simple geometry is one cube representing the table top and four cubes representing the legs. The table has one parameter to modify the width and two parameters for the table top material and the legs material.

```javascript
{
    "id": "demoCatalogId:DEMO_Desk",
    "labels": {
        "en": "Desk"
    },
    "parameters": [
        {
            "defaultValue": 1600,
            "enabled": "true",
            "key": "width",
            "labels": {
                "en": "Width"
            },
            "type": "Decimal",
            "unitType": "length",
            "validValues": [
                1400,
                1600,
                1800,
                2000
            ],
            "visible": "true",
            "visibleInPartList": "true"
        },
        {
            "defaultValue": 800,
            "key": "depth",
            "type": "Decimal",
            "visible": "false",
            "visibleInPartList": "false"
        },
        {
            "defaultValue": 700,
            "key": "height",
            "type": "Decimal",
            "visible": "false",
            "visibleInPartList": "false"
        },
        {
            "defaultValue": "demoCatalogId:6K",
            "enabled": "true",
            "key": "tableSurface",
            "labels": {
                "en": "Table Top Surface"
            },
            "type": "Material",
            "validValues":[
                "demoCatalogId:6K",
                "demoCatalogId:BN"
            ],
            "visible": "true",
            "visibleInPartList": "true"
        },
        {
            "defaultValue": "demoCatalogId:BN",
            "enabled": "true",
            "key": "legSurface",
            "labels": {
                "en": "Leg Surface"
            },
            "type": "Material",
            "validValues":[
                "demoCatalogId:6K",
                "demoCatalogId:BN"
            ],
            "visible": "true",
            "visibleInPartList": "true"
        },
        {
            "defaultValue": 1,
            "key": "showRightLeg",
            "type": "Decimal",
            "visible": "false",
            "visibleInPartList": "false"
        },
        {
            "defaultValue": 1,
            "key": "showLeftLeg",
            "type": "Decimal",
            "visible": "false",
            "visibleInPartList": "false"
        },
        {
            "defaultValue": 1,
            "type": "Decimal",
            "key": "allowRightDocking",
            "visible": "false",
            "visibleInPartList": "false"
        },
        {
            "defaultValue": 1,
            "type": "Decimal",
            "key": "allowLeftDocking",
            "visible": "false",
            "visibleInPartList": "false"
        }
    ],
    "subComponents": [],
    "geometry": "
        legThickness = 50;
        BeginObjGroup(group_table);
            # table top
            Cube(Vector3f{width, depth, legThickness});
                MoveMatrixBy(Vector3f{0,0,height});
                SetObjSurface(tableSurface);

            # table legs for left side
            if (showLeftLeg == 1) {
                BeginObjGroup(group_legs_left);
                    Cube(Vector3f{legThickness, legThickness, height});
                        MoveMatrixBy(Vector3f{0,0,0});
                    Copy();
                        MoveMatrixBy(Vector3f{0,depth - legThickness,0});
                EndObjGroup();
                    SetObjSurface(legSurface);
            }

            if (showRightLeg == 1) {
                BeginObjGroup(group_legs_right);
                    Cube(Vector3f{legThickness, legThickness, height});
                        MoveMatrixBy(Vector3f{0,0,0});
                    Copy();
                        MoveMatrixBy(Vector3f{0,depth - legThickness,0});
                EndObjGroup();
                    RotateMatrixBy(Vector3f{0,0,1},Vector3f{0,0,0},180);
                    MoveMatrixBy(Vector3f{width,depth,0});
                    SetObjSurface(legSurface);
            }
        EndObjGroup();
    ",
    "parentDockings": {
        "points": [
            {
                "position": "{0,0,0}",
                "mask": "demoCatalogId:PedestalToTableLeft",
                "selfAssignments": {
                    "onDock": {
                        "showLeftLeg": "0"
                    },
                    "onUnDock": {
                        "showLeftLeg": "1"
                    },
                    "onUpdate": {
                        "showLeftLeg": "0"
                    }
                },
                "rotation": "{0,0,0}",
                "condition": "(allowLeftDocking == 1)"
            },
            {
                "position": "{width,0,0}",
                "mask": "demoCatalogId:PedestalToTableRight",
                "selfAssignments": {
                    "onDock": {
                        "showRightLeg": "0"
                    },
                    "onUnDock": {
                        "showRightLeg": "1"
                    },
                    "onUpdate": {
                        "showRightLeg": "0"
                    }
                },
                "rotation": "{0,0,0}",
                "condition": "(allowRightDocking == 1)"
            },
            {
                "position": "{width,0,0}",
                "mask": "demoCatalogId:TableToTable",
                "rotation": "{0,0,0}"
            }
        ]
    },
    "childDockings": {
        "points": [
            {
                "position": "{0,0,0}",
                "mask": "demoCatalogId:TableToTable",
                "rotation": "{0,0,0}"
            }
        ]
    },
    "articleNr":"
        if (width > 1600) {
            articleNr = 'ABC';
        } else {
            articleNr = 'DEF';
        }
    ",
    "pricing": [
        {
            "region": "default",
            "currency": "EUR",
            "price": "1000"
        }
    ],
    "possibleChildren": [
        {
            "componentId":"demoCatalogId:DEMO_Pedestal"
        },
        {
            "componentId":"demoCatalogId:DEMO_Desk"
        },
        {
            "itemId":"demoCatalogId:DEMO_Desk"
        }
    ]
}
```

There are a few things to note in this script:
* 'id': The id of the product has to be unique per catalog.
* 'labels': Labels with the following keys are valid: 'en', 'de', 'zh-Hans' ,'it' ,'fr' ,'es' ,'hi' ,'ja' ,'pt' ,'pt-BR' ,'pl' ,'ru'
* 'parameters': Parameters are displayed inside the configurator. Optionally images can be defined as thumbnails for parameter values. You can define if this parameter should show up in the part list by setting the property 'visibleInPartlist'.
* 'articleNr': Define the article number of the product using 'articleNr'. You can add logic (conditions) here.
* 'pricing': Works similar to 'articleNr'.
* 'geometry': The geometrie part contains all the information which is required for rendering the object into a 3d szene. Geometric basic primitives as cube, sphere, rectangle and meshes can be used. We can add logic inside the 'geometry' section to display the product in a different way according to the selected parameter values. We need to create a material in the catalog before we can use it inside the component definition. To add materials you need access to the Roomle Pro admin interface where you can maintain your catalogs.


#### Docking components to components
A pedestal can be docked to the table. When the pedestal is docked, the legs of the table are hidden on the corresponding side. When the pedestal is docked to the table a connector is shown. There can only be docked one pedestal to a table at a time. The number of drawers can be switched between 2 and 3. This is why we add a parameter for the number of drawers.

![The pedestal component](figures/pedestal.png)

```javascript
{
    "id": "demoCatalogId:DEMO_Pedestal",
    "labels": {
        "en": "Pedestal"
    },
    "parameters": [
        {
            "defaultValue": 500,
            "enabled": "false",
            "key": "width",
            "type": "Decimal",
            "visible": "false",
            "visibleInPartList": "false"
        },
        {
            "defaultValue": 750,
            "enabled": "false",
            "key": "depth",
            "type": "Decimal",
            "visible": "false",
            "visibleInPartList": "false"
        },
        {
            "defaultValue": 566,
            "enabled": "false",
            "key": "height",
            "type": "Decimal",
            "visible": "false",
            "visibleInPartList": "false"
        },
        {
            "defaultValue": 3,
            "enabled": "true",
            "key": "drawers",
            "labels": {
                "en": "Drawers"
            },
            "type": "Count",
            "validValues": [
                2,
                3
            ],
            "conditionalValues": [],
            "visible": "true"
        },
        {
            "defaultValue": 0,
            "key": "hasConnector",
            "type": "int",
            "visible": "false",
            "visibleInPartList": "false"
        }
    ],
    "geometry": "
        boardThickness = 18;
        BeginObjGroup(group_pedestal);
            # back
            AddCube(Vector3f{width - boardThickness * 2, boardThickness, height - boardThickness},Vector2f{1,1},-90,Vector2f{0,0});
                MoveMatrixBy(Vector3f{boardThickness,0,0});

            AddCube(Vector3f{boardThickness, depth - (boardThickness + 2), height - boardThickness},Vector2f{1,1},-90,Vector2f{0,0});
                MoveMatrixBy(Vector3f{0,0,0});

            Copy();
                MoveMatrixBy(Vector3f{width-boardThickness,0,0});

            # top
            AddCube(Vector3f{width, depth, boardThickness},Vector2f{1,1},90,Vector2f{0,0});
                MoveMatrixBy(Vector3f{0,0,height-boardThickness});

            if (drawers == 2) {
                Cube(Vector3f{width, boardThickness, 360});
                    MoveMatrixBy(Vector3f{0,depth - boardThickness,0});

                Cube(Vector3f{width, boardThickness, 180});
                    MoveMatrixBy(Vector3f{0,depth - boardThickness,(362 + 2)});
            }
            if (drawers == 3) {
                BeginObjGroup(group_drawer);
                    Cube(Vector3f{width, boardThickness, 180});
                        MoveMatrixBy(Vector3f{0,depth-boardThickness,0});
                EndObjGroup();
                Copy();
                    MoveMatrixBy(Vector3f{0,0,182});
                Copy();
                    MoveMatrixBy(Vector3f{0,0,182});
            }
            if (hasConnector == 1) {
                BeginObjGroup(group_connector);
                    Cylinder(100, 100, 4, 20, 1, 1);
                    Cylinder(30, 30, 113, 20, 1, 1);
                        MoveMatrixBy(Vector3f{0,0,4});
                    Cube(Vector3f{140, 563, 40});
                        MoveMatrixBy(Vector3f{-140/2,-563/2,117});
                EndObjGroup();
                    MoveMatrixBy(Vector3f{width/2,depth/2,(180 + 2) * 3 + boardThickness});
            }
        EndObjGroup();
            SetObjSurface('demoCatalogId:6K');
            MoveMatrixBy(Vector3f{0,-depth,0});
    ",
    "childDockings": {
        "points": [
            {
                "position": "{0,-depth,0}",
                "mask": "demoCatalogId:PedestalToTableLeft",
                "rotation": "{0,0,0}",
                "assignmentsOnDock":{
                    "allowRightDocking": "0"
                },
                "assignmentsOnUnDock":{
                    "allowRightDocking": "1"
                },
                "assignmentsOnUpdate":{
                    "allowRightDocking": "0"
                },
                "selfAssignments": {
                    "onDock": {
                        "hasConnector": "1"
                    },
                    "onUnDock": {
                        "hasConnector": "0"
                    },
                    "onUpdate": {
                        "hasConnector": "1"
                    }
                }
            },
            {
                "position": "{width,-depth,0}",
                "mask": "demoCatalogId:PedestalToTableRight",
                "rotation": "{0,0,0}",
                "assignmentsOnDock":{
                    "allowLeftDocking": "0"
                },
                "assignmentsOnUnDock":{
                    "allowLeftDocking": "1"
                },
                "assignmentsOnUpdate":{
                    "allowLeftDocking": "0"
                },
                "selfAssignments": {
                    "onDock": {
                        "hasConnector": "1"
                    },
                    "onUnDock": {
                        "hasConnector": "0"
                    },
                    "onUpdate": {
                        "hasConnector": "1"
                    }
                }
            }
        ]
    },
    "articleNr":"
        if (drawers == '2') {
            articleNr = 'UVW';
        } else {
            articleNr = 'XYZ';
        }
    ",
    "pricing": [
        {
            "region": "default",
            "currency": "EUR",
            "price": "
                if (drawers == '2') {
                    price = 830;
                } else {
                    price = 535;
                }
            "
        }
    ]
}
```

Notice this values to allow docking between components:

* 'validChildren': We need to add componentIds into 'validChildren'. The configurator loads all components listed in 'validChildren' and displays them in the add-ons section of the menu.
* 'parentDockings': Define a mask and the position inside the coordinate system of this component (which is the 'parent' during the process of docking) where you want the child to be docked to. You can pass values from the parent to the child on docking, on undocking and on update using assignments.
* 'childDockings': Define a mask to match with parentDockings. Define the position inside the child component's coordinate system where you want the parent to be docked. On dock, on undock and on update assignments are available too.
* 'selfassignments': You can change values of this component on dock, on undock or on update using 'selfassignments'.

As a result the table with docked pedestal looks like this:

![The pedestal docked to the table](figures/docked.png)

#### Using subComponents
In some cases multiple instances of one component might be handy (i.e. legs of the table). In this example we want to create a component and reuse it inside the geometry section. Two legs are built as subcomponents to allow reuse for both sides of the table.

* The subcomponent is a separate component:

```javascript
{
    "id": "demoCatalogId:DEMO_Leg",
    "labels": {
        "en": "Desk leg"
    },
    "parameters": [
        {
            "defaultValue": 50,
            "key": "legThickness",
            "type": "Decimal",
            "visible": "true",
            "visibleInPartList": "false"
        },
        {
            "defaultValue": "demoCatalogId:BN",
            "enabled": "true",
            "key": "legSurface",
            "labels": {
                "en": "Leg Surface"
            },
            "type": "Material",
            "validValues":[
                "demoCatalogId:6K",
                "demoCatalogId:BN"
            ],
            "visible": "true",
            "visibleInPartList": "false"
        }
    ],
    "geometry": "
        legThickness = 50;
        BeginObjGroup(group_legs);
            Cube(Vector3f{legThickness, legThickness, height});
                MoveMatrixBy(Vector3f{0,0,0});
            Copy();
                MoveMatrixBy(Vector3f{0,depth - legThickness,0});
        EndObjGroup();
            SetObjSurface(legSurface);
    ",
    "articleNr":"",
    "pricing": []
}
```

To use the subcomponent we need to modify some parts of the DEMO_Desk component:

```javascript
"subComponents": [
    {
        "internalId":"DEMO_Leg",
        "componentId":"demoCatalogId:DEMO_Leg",
        "assignments": {
            "legThickness": "50",
            "legSurface": "legSurface"
        },
        "numberInPartList":"0"
    }
],
"geometry": "
    legThickness = 50;
    BeginObjGroup(group_table);
        # table top
        Cube(Vector3f{width, depth, legThickness});
            MoveMatrixBy(Vector3f{0,0,height});
            SetObjSurface(tableSurface);

        # table legs for left side
        if (showLeftLeg == 1) {
            SubComponent(DEMO_Leg);
        }

        if (showRightLeg == 1) {
            SubComponent(DEMO_Leg);
                RotateMatrixBy(Vector3f{0,0,1},Vector3f{0,0,0},180);
                MoveMatrixBy(Vector3f{width,depth,0});
        }
    EndObjGroup();
",
```

There are some things to note here:
* 'internalId': We use the internalId to define the subcomponent inside the subComponents section and add SubComponent(DEMO_Leg) to the geometry section to add the geometry of the legs at the specified position.
* 'assignments': legThickness and legSurface are assigned automatically on geometry update. The value selected in the desk is passed on to the subcomponent.
* the values for 'articleNr' and 'price' are automatically summed up for the total partlist when subcomponents are used and numberInPartList is > 0.




# <a name="configuratorFormat"></a>Roomle Configuration Format

## Coordinate System
The Roomle configurator uses
* a left-handed cartesian coordinate system and
* points and sizes in mm

## Component vs. Configuration

### Component
A component is the basic element used for configurable objects. Each component contains a definition on how it can be configured and used within configurations. Every component must have an unique identifier. Components can be seen as templates for the basic elements of configurable objects.

### Configuration
The configuration defines the actual variant of the involved components used in the configuration. The user can create variants by changing the parameters or by combining additional components (add-on component) with the existing component. Every configuration must have exactly one root-component. Add-on components can be docked to the working configuration. Values can be passed between parents and children, siblings, to the component itself to validate or update conditions using assignments.

### Material
Within each catalog you can define materials to be used in the geometry-script of a component. Each material can have an assigned texture to be used. Every Material must have an unique identifier.

### Catalogs
In the Roomle Configurator items, components, configurations and materials are organized in catalogs.



# <a name="componentDefinition"></a>Component Definition
A component definition contains parameters, the logic how to interact with other components, the price and article number and how a product is displayed inside the configurator.


Basically it is a file in json format extended with some parts of Roomle Script representing the logic and geometry commands. A component definition includes the following values:

### An unique identifier *id*

```javascript
"id": "sampleCatalog:component1",
```

### *Labels* of the component for multiple languages

```javascript
"labels" : {
    "en" : "English Label" ,
    "de" : "Label auf Deutsch"
},
```

### A list of *parameters* with label, possible values and a default value.

```javascript
"parameters" : [
    {
        "key": "door",
        "labels": {
            "en": "Elementtype",
            "de": "Fachtyp"
        },
        "type": "String",
        "valueObjects": [
            {
                "value": "None",
                "labels": {
                    "en": "None",
                    "de": "Keine"
                },
                "thumbnail":"http://url.to.image/iconImage.png"
            },
            {
                "value": "Slide-in door",
                "labels": {
                    "en": "Slide-in door",
                    "de": "Einschubtüre"
                },
                "condition":"(width == 750 && (depth == 500 || depth == 350)) || (width == 500 && depth == 500)",
                "thumbnail":"http://url.to.image/iconImage.png"
            }
        ],
        "defaultValue": "None",
        "sort":1,
        "highlighted":true,
        "enabled": "true",
        "visible": "true",
        "visibleInPartList": "(material == 'sampleCatalog:materialId2')"
    },
    {
        "key": "width",
        "labels": {
            "en": "Width",
            "de": "Breite"
        },
        "type": "Integer",
        "unitType": "length",
        "defaultValue": 250,
        "validValues": [
            250,
            350
        ],
        "conditionalValues": [
            {
                "value": 500,
                "condition": "(colorMaterial == 'sampleCatalog:materialId1')"
            },
            {
                "value": 750,
                "condition": "(colorMaterial == 'sampleCatalog:materialId2')"
            }
        ],
        "enabled": "true",
        "visible": "true"
    },
    {
        "defaultValue": "demoCatalogId:materialId",
        "key": "colorMaterial",
        "labels": {
            "en": "Color",
            "de": "Farbe"
        },
        "type": "Material",
        "validGroups": [
            "demoCatalogId:metalColors"
        ],
        "validValues":[
            "demoCatalogId:materialId1",
            "demoCatalogId:materialId2"
        ],
        "enabled": "true",
        "visible": "true"
    }
],
```

You can use the following options:
```javascript
"language" : "en"|"de"| ...
"type": "Integer"|"Decimal"|"String"|"Boolean"|"Material"
"unitType": "length"|"area"|"count"|"angle"
"value": long|float|String|true|false
```

### A list of possible dockings *parentDocking* and *childDocking*.
These are the connectors to other component instances.

```javascript
"parentDockings" : {
    "points" : [ {
            "position" : "{0,0,0}" ,
            "mask" : "DockingMask1" ,
            "rotation" : "{0,0,0}" ,
            "assignmentsOnDock":{
                "allowRightDocking": "0"
            },
            "assignmentsOnUnDock":{
                "allowRightDocking": "1"
            },
            "assignmentsOnUpdate":{
                "allowRightDocking": "0"
            },
            "selfAssignments": {
                "onDock": {
                    "hasConnector": "1"
                },
                "onUnDock": {
                    "hasConnector": "0"
                },
                "onUpdate": {
                    "hasConnector": "1"
                }
            }

        },...
    ],
    "lines" : [ {
            "position": "{(-Breite/2),0,-42.5}",
            "mask" : "DockingMask2" ,
            "assignmentsOnDock":{
                "allowRightDocking": "0"
            },
            "assignmentsOnUnDock":{
                "allowRightDocking": "1"
            },
            "assignmentsOnUpdate":{
                "allowRightDocking": "0"
            },
            "rotation": "{0,-(0),-90}",
            "positionTo": "{(Breite/2),0,-42.5}"
        },...
    ],
    "ranges" : [ {
            "position": "{0,-(-34.5),1100}",
            "mask" : "DockingMask1" ,
            "assignmentsOnDock":{
                "allowRightDocking": "0"
            },
            "assignmentsOnUnDock":{
                "allowRightDocking": "1"
            },
            "assignmentsOnUpdate":{
                "allowRightDocking": "0"
            },
            "rotation" : "{0,0,0}",
            "stepEnd" : "{0,34.5,2200}",
            "stepX" : "0",
            "stepY" : "0",
            "stepZ" : "488"
        },...
    ],
    "lineRanges" : [ {
            "position": "{(-width/2),0,-42.5}",
            "mask" : "DockingMask2",
            "assignmentsOnDock":{
                "allowRightDocking": "0"
            },
            "assignmentsOnUnDock":{
                "allowRightDocking": "1"
            },
            "assignmentsOnUpdate":{
                "allowRightDocking": "0"
            },
            "rotation" : "{0,0,-90}",
            "positionTo": "{(width/2),0,-42.5}",
            "stepEnd" : "{0,34.5,2200}",
            "stepX" : "0",
            "stepY" : "0",
            "stepZ" : "488"
        },...
    ]
},
```

```javascript
"childDockings" : {
    "points": [ ... ]
    ...
    # similar to parentDockings
},
```

A note to the docking logic: The positions should be stated with 2 digits floating point precision. When matching the points with given docking points from the component definition, they are rounded to 1 digit.

### A list of *sibling points*
These are connectors between component instances without parent and child relationship.

```javascript
"siblings":[
    {
        "position": "{width,0,0}",
        "mask": "catalogId:componentToComponentSibling",
        "assignmentsOnDock": {"hasSiblingLeft": "1"},
        "assignmentsOnUnDock": {"hasSiblingLeft": "0"},
        "assignmentsOnUpdate": {"hasSiblingLeft": "1"}
    },
    {
        "position": "{0,0,0}",
        "mask": "catalogId:componentToComponentSibling",
        "assignmentsOnDock": {"hasSiblingRight": "1"},
        "assignmentsOnUnDock": {"hasSiblingRight": "0"},
        "assignmentsOnUpdate": {"hasSiblingRight": "1"}
    }
],
```

### A list of possible *subcomponents*
to encapsulate and reuse geometry script, partlist and price calculation.

```javascript
"subComponents" : [
    {
        "internalId" : "leftSide",
        "componentId" : "catalogId:sideComponentLeft",
        "assignments" : {
            "length" : "length"
        },
        "numberInPartList" : "3"
    },
    {
        "internalId" : "rightSide",
        "componentId" : "catalogId:sideComponentRight",
        "assignments" : {
            "length" : "length * 2"
        },
        "numberInPartList" : "
            if (length > 10) {
                number = 2;
            } else {
                number = 1;
            }
        "
    },...
]
```

### A list of possible children
containing componentIds and optional a condition when this child is possible.

```javascript
"validChildren" : [
    "catalogId:subComponentId1",
    "catalogId:subComponentId2"
]
```

### An *article number* assignment or script.
directly assign a value
```javascript
"articleNr" : "article1234",
```
assign a value using Roomle script to add article number logic
```javascript
"articleNr" : "
    if (legType == 'Fixed') {
        if (topSurface == 'catalogId:materialId3') {
            articleNr = 'ArticleNrXYZ';
        } else {
            articleNr = 'ArticleNrUVW';
        }
    } else {
        articleNr = 'ArticleNrABC';
    }
",
```


### *Price* calculation script

```javascript
"price" : 99.90,
```

```javascript
"pricing" : [
    {
        "region" : "default",
        "currency" : "EUR",
        "price" : "
            if (screenSurfaceParent == 'catalogId:materialId4') {
                price = 1568.00;
            }
        "
    }
],
```

The following values are valid:
```javascript
"currency" : "EUR"|"GBP"|"USD"| ...
"region" : "default"|"at"|"uk"|"us"| ...
```


### A geometry script written in RoomleScript.

```javascript
"geometry" : "<geometry skript>"
```

A geometry in Roomle script could look like this:
```javascript
"geometry": "
    # define variables to use inside the geometry context
    depth = 200;
    offset = 50;

    # create a group of geometry objects
    BeginObjGroup('groupName');
        # geometry call to create a cube width and height are the keys of parameters defined as "parameters"
        AddCube(Vector3f{width, depth, height});

        # conditions and loops are available in Roomle script
        if (hasRightNeighbor == 0) {
            Copy();
                MoveMatrixBy(Vector3f{width,0,0});
        } else {
            Copy();
                RotateMatrixBy(Vector3f{0,0,1},Vector3f{0,0,0},90);
        }
        if (level == 0) {
            # create a subcomponent 'templateSub'
            SubComponent(templateSub);
        }
        for (i=0; i<3; i=i+1) {
            Copy();
                MoveMatrixBy(Vector3f{0,0,offset});
        }
    EndObjGroup();
        # these transformations apply on the whole group
        SetObjSurface(colorMaterial);
        MoveMatrixBy(Vector3f{0,0,offset});
        RotateMatrixBy(Vector3f{0,0,1},Vector3f{0,0,0},90);

    AddCube(...);
",
```

## Geometry Functions

### *BeginObjGroup*
begins a new objectgroup
```javascript
BeginObjGroup(GroupName:String);

BeginObjGroup('groupName');
```

### *EndObjGroup*
defines the end of the objectgroup
```javascript
EndObjGroup(GroupName:String);

EndObjGroup('groupName');
EndObjGroup(); # works too
```

### *MoveMatrixBy*
moves the previously drawn object or group by the given vector
```javascript
MoveMatrixBy(Translation:Vector3f{x,y,z});

MoveMatrixBy(Vector3f{100,70,50});
```

### *RotateMatrixBy*
rotates the last drawn object or group according to the parameter
```javascript
RotateMatrixBy(RotationAxis:Vector3f{x,y,z}, Pivot:Vector3f{x,y,z}, Angle:float);

RotateMatrixBy(Vector3f{0,0,1},Vector3f{0,0,0},90);
```

### *SetObjSurface*
assigns the given Material to the last drawn object or group
```javascript
SetObjSurface(MaterialId:String);

SetObjSurface(colorMaterial);
```
You can use the key of a *parameter* with type *material*.

### *Copy*
creates a copy of the last drawn object or group
```javascript
Copy();
```

### *SubComponent*
injects the geometry of the referenced subcomponent
```javascript
SubComponent(internalId:String);

SubComponent(subComponentId);
```
The id must be defined in the section *subComponents*.


## Geometric basic primitives

To draw geometry inside the Roomle Configurator you can use basic primitives: rectangle, cube, sphere, cylinder, prism and mesh.

### Rectangle
```javascript
AddRectangle(Size:Vector2f{width,height},
        *uvScale:Vector2f{scaleU, scaleV } *uvRotation:float,
        *uvOffset:Vector2f{offU, offV });

AddRectangle(Vector2f{100, 70});
```
### Cube
```javascript
AddCube(Size:Vector3f{width,depth,height},
        *uvScale:Vector2f{scaleU, scaleV } *uvRotation:float,
        *uvOffset:Vector2f{offU, offV });

AddCube(Vector3f{100, 70, 150});
```

### Sphere
```javascript
AddSphere(Size:Vector3f{width,depth,height},
        *uvScale:Vector2f{scaleU, scaleV } *uvRotation:float,
        *uvOffset:Vector2f{offU, offV });

AddSphere(Vector3f{100, 100, 100});
```

### Cylinder
```javascript
AddCylinder(RadiusBottom:float,
            RadiusTop:float,
            Height:float,
            CircleSegments:float,
            *uvScale:Vector2f{scaleU, scaleV } *uvRotation:float,
            *uvOffset:Vector2f{offU, offV });

AddCylinder(100,
            80,
            120,
            20);
```

### Prism
```javascript
# Creates an extruded object given the 2d shape and the height
AddPrism(Height:float,
        Vertices:Array<Vector2f>,
        *uvScale:Vector2f{scaleU, scaleV } *uvRotation:float,
        *uvOffset:Vector2f{offU, offV });

AddPrism(height,
        [Vector2f{0, 0},
        Vector2f{0, 50},
        Vector2f{25, 50},
        Vector2f{0, 15}]);
```

Creates a mesh from the given vertices. If no indices are provided, the number of points must be a multiple of 3. Every group of 3 vertices forms a triangle. With indices there must not be an index bigger than the number of points -1. The number of indices must be a multiple of 3.

### Mesh
```javascript
AddMesh(Vertices:Array<Vector3f>,
    *Indices:Array<int>,
    *uvScale:Vector2f{scaleU, scaleV},
    *uvRotation:float,
    *uvOffset:Vector2f{offU, offV})

AddMesh(Vector3f[{0, 0, 0}, {0, 100, 0}, {0, 100, 100}]);
```

Creates a mesh from the given vertices with uvCoordinates and normals
```javascript
AddMesh(Vertices:Array<Vector3f>,
        *Indices:Array<int>,
        *uvCoordinates:Array<Vector2f>,
        *normals:Array<Vector3f>)

AddMesh(Vector3f[
    {0,0,side/2},
    {side,0,side/2},
    {side,side,side/2},
    {0,side,side/2},
    {side/10,side/10,0},
    {side-side/10,side/10,0},
    {side-side/10,side-side/10,0},
    {side/10,side-side/10,0}],
    [0,1,2, 0,2,3, 6,5,4, 6,4,7,
    0,4,5, 1,6,2, 1,5,6, 2,7,3,
    2,6,7, 3,4,0, 3,7,4],
    Vector2f{2,1},
    45,
    Vector2f{100,0});
```




