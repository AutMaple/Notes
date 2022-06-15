# Drag and Drop

### Events for Drag & Drop Operations

- *DragEnter* 事件在 Drag Source 的边界与 Drop target 控件的边界相交的时候会触发

- *DragOver* 事件在 Drag Source 在 Drop target 控件上拖动时会连续的触发该事件
- *DragLeave* 事件在控件被拖出目标边界但又没有被放下时触发
- *Drop* 事件在 Drag Source 放置在 Drop target 时触发该事件

- The DragEnter event occurs when the data is dragged into the drop target's boundary.
- The DragOver event occurs continuously while the data is dragged over the drop target.
- The DragLeave event occurs when the data is dragged out of the target's boundary without being dropped.
- The Drop event occurs when the data is dropped over the drop target.

# Drag and Drop Operation

Drag and Drop 操作涉及到两个部分: 一个是 Drag Source，即拖动的是哪个控件。一个 Drop Target，即将控件拖动到哪个目标位置。Drag Source 和 Drop target 可能是同一个应用程序中的控件，也可能是不同应用程序中的控件。同时拖动和放置的控件的类型以及控件的数量是任意的，没有限制。

- 拖动和放置操作支持在不同应用程序之间进行
- 拖动和放置操作支持 WPF 应用程序和其他的窗体应用程序进行交互

Drag-and-drop operations typically involve two parties: a drag source from which the dragged object originates and a drop target which receives the dropped object.

The drag source and drop target may be UI elements in the same application or a different application. The type and number of objects that can be manipulated with drag-and-drop is completely arbitrary

- Drag-and-drop supports manipulating objects within a single application, or between different applications.
- Dragging-and-dropping between WPF applications and other Windows applications is also fully supported

## Data and Data Object

数据传输作为拖动放置操作中一部分，传输的数据被存放在一个数据对象中。理论上，一个数据对象由下面一个或多个部分组成

- 包含真实数据的对象
- 对应数据的格式化标识符

数据本身是可以被任何代表基类的对象组成，数据对应的格式则是提供数据怎样格式化的一个字符串或者 *Type* 类型。数据对象支持持有多个数据或数据格式化对。这个特性使得单个的数据可以提供多种数据格式

Data that is transferred as part of a drag-and-drop operation is stored in a data object. Conceptually, a data object consists of one or more of the following pairs:

- An Object that contains the actual data.
- A corresponding data format identifier.

The data itself can consist of anything that can be represented as a base Object. The corresponding data format is a string or Type that provides a hint about what format the data is in. Data objects support hosting multiple data/data format pairs; this enables a single data object to provide data in multiple formats.

## Grag and Drop Steps

1. 拖动事件是由 *MouseMove* 和 *MouseLeftButtonDown* 事件组合而成的，我们可以单独的注册这两个事件，也可以使用这两个事件的组合事件: *PreviewMouseLeftButtonDown*
2. 找到要拖动的数据并创建一个 *DataObject* 对象，用这个对象来保存拖动对象的格式，数据和其他的操作。例如 WPF 控件的数据；文件，文件夹的数据等等
3. 在拖动事件对应的业务处理代码中初始化步骤 2 中的东西。
4. 在 Drop target 对应的控件中设置 *AllowDrop* 为 *true*
5. 在 Drop target 对应的控件上注册一个 *DragEnter* 事件来检测拖动事件
   - 调用 event 参数中的 *GetDataPresent* 方法来检查数据和格式
   - 如果数据是可以被拖动的，那就设置 event 参数中的 *Effect* 属性来显示合适的鼠标样式
6. 在用户松开鼠标左键时，DragDrop 事件被触发
   - 调用 event 参数中 Data Object 的 *GetData* 方法获取数据
   - 将获取到的数据添加到对应的控件中

For example, files, folders, and selections of content are some of the more common objects manipulated through drag-and-drop operations.

- Step 1. Detect a drag as a combination of *MouseMove* and *MouseLeftButtonDown* events, use *PreviewMouseLeftButtonDown* event
- Step 2. Find the data you want to drag and create a `DataObject` that contains the format, the data and the allowed effects, such as Data of WPF Controls, File or Folder, HTML...
- Step 3. Initiate the dragging by calling DoDragDrop ()
- Step 4. Set the AllowDrop property to True on the elements you want to allow dropping.
- Step 5. Register a handler to the DragEnter event to detect a dragging over the drop location.
  - Check the format and the data by calling GetDataPresent () on the event args argument.
  - If the data can be dropped, set the Effect property on the event args argument to display the appropriate mouse cursor.
- Step 6. When the user releases the mouse button the DragDrop event is called.
  - Get the data by calling the GetData () method on the Data object provided in the event args argument.
  - Add this data to WPF Controls.

## Data Transfer

Drag-and-drop is part of the more general area of data transfer. **Data transfer includes drag-and-drop and copy-and-paste operations.**

A drag-and-drop operation is analogous to a copy-and-paste or cut-and-paste operation that is used to transfer data from one object or application to another by using the system clipboard.

Both types of operations require:

1. A source object that provides the data
2. A way to temporarily store the transferred data
3. A target object that receives the data

## Drag and Drop Events

Drag-and-drop operations support an event driven model. Both the drag source and the drop target use a standard set of events to handle drag-and-drop operations.

### Drag Operation

当一个拖动的物体接触到 Drop target 边界的时候，Drag 事件就会出发，该系列事件是冒泡事件

要开始拖动事件，需要检测鼠标左键按下时鼠标的移动事件，即：注册 *MouseMove* 和 *MouseLeftButtonDown* 事件

当拖动事件初始化后，需要指定要拖动的数据，例如：拖动 *ListViewItem* 中的数据，我们可以在鼠标事件的 *originalSource* 属性找到 *ListViewItem* 

This event occurs when an object is dragged into the drop target's boundary. This is so-called a bubbling event.

To start the drag operation, we have to detect a mouse move while the left mouse button is pressed.

- To do this we have to hook up handlers on the **MouseMove and MouseLeftButtonDown events**.

When the drag is initiated, we need to specify the data we want to drag.

- Example: data of the ListViewItem we dragged. We find the ListViewItem in the OriginalSource of the mouse event args.

### Drop Operation

为了让一个元素能够成为 Drop target，需要在对应的控件中设置 *AllowDrop* 属性

- 当用户拖动控件到 Drop target 元素的上方时，会触发 *DragEnter* 事件
- 在 *DragEnter* 事件中我们可以对数据进行分析，然后判断对应的数据是否可以被放置在该控件中 

当用户释放鼠标左键时，*Drop* 事件被触发，并且拖动的数据被保存在 event 参数的 *DataObject* 属性上

This event occurs when an object is dropped on the drop target. This is a bubbling event. **To make an element be a drop location, set the AllowDrop property to true.**

- When the user drags an item over the element, the DragEnter event is called.
- In this event you can analyze the data and decide if a drop is allowed or not. 

When the user releases the mouse button, the Drop event is called.

- **The data is available in the DataObject provided in the DragEventArgs**.

> **Dragging is initiated by calling the DoDragDrop method for the source control.**
>
> The DoDragDrop method takes two parameters: data, specifying the data to pass allowedEffects, specifying which operations (copying and/or moving) are allowed
>
> A new DataObject object is automatically created. This in turn raises the GiveFeedback event. In most cases you do not need to worry about the GiveFeedback event, but if you wanted to display a custom mouse pointer during the drag, this is where you would add your code.
>
> Any control with its AllowDrop property set to True is a potential drop target. The AllowDrop property can be set in the Properties window at design time, or programmatically in the Form_Load event.
>
> **As the mouse passes over each control, the DragEnter event for that control is raised**. The GetDataPresent method is used to make sure that the format of the data is appropriate to the target control, and the Effect property is used to display the appropriate mouse pointer.
>
> **If the user releases the mouse button over a valid drop target, the DragDrop event is raised.** Code in the DragDrop event handler extracts the data from the DataObject object and displays it in the target control.

- SetData ( Type format, object data ) /// format is the "format" of the day you are passing ( e.g. Formats.Text, Formats.Image, etc.. ) you can pass any custom types.

- GetDataPresent ( Type format ) /// is what the drop target will use to inquire and extract the data .. if it is a type it can handle, it will call GetData () and handle it ..

# 参考文章

- [[WPF handle drag and drop as well as left click](https://stackoverflow.com/questions/12802122/wpf-handle-drag-and-drop-as-well-as-left-click)](https://stackoverflow.com/questions/12802122/wpf-handle-drag-and-drop-as-well-as-left-click)
