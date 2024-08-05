## HMI A

### Contents
1. [What's new?](#whats_new)
2. [Responsive Layouts](#responsive)
3. [Developer Tools](#developer_tools)
4. [Reusability](#reusability)
5. [Tabular Data](#tables)
6. [Server Config](#server)

<a id="whats_new"></a>

### 1. What's new?

1.14 Presentation key points:
- Compatability & Package Manager
- **Exercise: Context object**
    - Add a simple control to view the axis status
    - Use the axis "Running" symbol for an indicator
- **Exercise: NC Viewer**
    - Drop in control and configure (check property tooltips)
    - Create 'NC' (500) and 'NC-Task' (501) ADS targets

<a id="responsive"></a>

### 2. Responsive Layouts

We frequently advertise TwinCAT HMI as "Responsive". This means that all the components on a page are accessible and appropriately organized regardless of the size, resolution or aspect ratio of the consuming client. TwinCAT HMI has two primary strategies for achieving this goal:

1. **Relative** size and position values:
    - In general, controls should not rely on **static** *position* (Top, Left, Right, Bottom) or *size* (Height, Width) properties. Instead, these properties should be **relative** to the rest of the controls within the layout and control hierarchy.
2. The Grid Control
    - The Grid control provides HMI developers with an easy way to dynamically reorient controls within a layout. It is a container control organized into rows and columns which can automatically wrap content based on minimum/maximum size thresholds.

>TwinCAT HMI has these tools for building responsive layouts, but it is still much easier to statically define all size/position values. This is the default behavior with the drag & drop designer. Responsive layouts require many more design considerations and forethought.

#### Exercise: Fix the Main page of the HMI application so that it is responsive.

Use the Grid control. Refine the column and row definitions to get an appropriate and responsive layout. Change controls with static size and position properties so they are more responsive. If something that needs to be centered must also remain a fixed size, you may need to implement a transformation "trick".

It is helpful to use the document outline

<a id="developer_tools"></a>

### 3. Developer Tools

The LiveView window has a number of useful tools within the 'Developer Tools' dialog (⚙️).

From the 'Elements' tab, you can explore all the markup and style sheets of the page. When a graphical component is not being rendered how we expect, we can modify the markup and stylesheet attributes directly from here.

#### Exercise: Open up LiveView and the developer console.
- Explore the DOM; generated markup and stylesheets
- Make some CSS or layout tweaks and observe the real time changes
- Modify the EventGrid control to customize the button layout
- Make these changes permanent by adding *selectors* and *attributes* to the project CSS file(s)

Another useful test and debug functionality is the `console` tab. We can execute JavScript right from within this window, with all the loaded references and resources from the framework.

- In the console, explore the `TcHmi.` namespace
```js
TcHmi.Server.getCurrentUser()
TcHmi.Symbol.read('Test', TcHmi.SymbolType.Internal)
```
- Get a control instance and read some property values
```js
const ctrl = TcHmi.Controls.get('TcHmiImage_1')
ctrl.getTop()
ctrl.getSrc()
```
- You can change any properties that are not read-only, and the framework will update the DOM. Note the property names and getter/setter method names:
```js
ctrl.setTop(50)
```
Note that we have modified the runtime version. Reloading the content file will restore the previous property value.

<a id="reusability"></a>

### 4. Reusability
As we continue to write more object-oriented PLC code, we can take similar approaches to component modularity from the HMI side as well. We can leverage User Controls to combine and parameterize common HMI controls for reusability.

The developer of our demo application has created a Function Block to handle similarly behaving Cylinders in their PLC. For the HMI, they have appropriately created a user control which captures all the necessary manual operations and indicators. 

*The FBs are stored in an array in the PLC, which may make it easier for us to reference them in the HMI. TwinCAT HMI does support indirect addressing via the same bracket [ ] notation you would use in the PLC... but there caveats!*

#### Exercise: Rework the Manual page to use fewer user control instances.

- Create a combobox control and populate it with a list of indices
- Bind an instance of the Cylinder user control using indirect addressing

Observe the 'function binding' issue with indirect addressing. We can use the bracket notation for **read-only** indicators, but read/write control must be explicitly bound. We can potentially address this by using the `CreateBinding` function, but we will still need to statically define our array indices. To make this truly dynamic, we will have to write some code.

- Our function will be very similar to `CreateBinding`, just with an extra *index* parameter
- Special handling:
    - Parameter types of `Control`, `ControlPropertyName`, `Symbol`, and `Number`
    - Parameter of type `Symbol` must be passed by reference
    - We will manually add `refTo` to the property name metadata

> When we go to call our function, make sure all of the bindings are globally accessible. For instance, the context object is "lost" and cannot be resolved from outside of the actions and conditions editor...

<a id="tables"></a>

### 5. Tabular Data

There are a couple options for visualizing tabular data in TwinCAT HMI. The quick and dirty way is to just use the *Object Browser* control. This automatically digests whatever structured data you throw at it, and allows for read/write access to all elements and members.

For more customization options, the *Data Grid* control can be used. This allows you to specify column definitions and custom data handling functions.

#### Exercise: Visualize the structure array data in an Object Browser control. Then, create a Data Grid control with some custom formatting and data handling columns.

<a id="server"></a>

### 6. Server Config
- Troubleshooting
    - Client and target license utilization
    - Log page for extension and dotnet errors
- Config Export/Import
    - Runtime user management, recipes