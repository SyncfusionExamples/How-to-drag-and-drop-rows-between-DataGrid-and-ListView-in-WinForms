# Drag and drop rows between Data Grid and List View in WinForms  

To perform dragging between the ListView and Data Grid, by using the [GridRowDragDropController.Drop](https://help.syncfusion.com/cr/windowsforms/Syncfusion.WinForms.DataGrid.Interactivity.RowDragDropController.Drop) event you can detect when rows are dropped into the grid from an outside source and handle insertion or removal from the source collection.

This sample demonstrates how to drag data between a Windows Forms ListView and an Syncfusion SfDataGrid. The example covers both directions: dragging rows from the ListView into the DataGrid and dragging rows from the DataGrid into the ListView. It also shows how to maintain the data model and remove the item from the original source after the drop operation completes.

## Sample overview

This sample is useful when you want to provide a seamless row drag-and-drop experience between two WinForms controls that represent data in different ways. The implementation uses the ListView drag/drop events and the SfDataGrid row drag/drop controller to identify the source, determine the target insertion position, and update the underlying data collection.

## Structure

- `Assets/DragDropBetweenControls_Image.png` - screenshot of the drag-and-drop sample.
- `README.md` - sample description and usage notes.
- Form files and supporting code - contains the WinForms controls, drag/drop event handlers, and data model for the sample.

## Prerequisites

Before running this sample, ensure that you have the following installed:

- Visual Studio 2019 or later.
- .NET Framework 4.6.1 or later.
- Syncfusion WinForms DataGrid package or assembly reference, matching your project configuration.
- Basic knowledge of WinForms drag-and-drop and `SfDataGrid` usage.

## Version

This sample has been created and validated with the Syncfusion WinForms DataGrid package used in WinForms projects. If you are using a different Syncfusion version, verify the API and namespace names in your installed package before building the sample.

## How it works

- The ListView begins the drag operation in the `ItemDrag` handler by calling `DoDragDrop`.
- The receiving ListView handles the drop by checking the incoming `IDataObject` for data from the DataGrid, then adds the record(s) and removes the original item if needed.
- The DataGrid handles the external drop using `GridRowDragDropController.Drop` and inserts the dropped rows into the `SourceCollection` at the appropriate index.
- The sample uses `OrderInfo` objects and stores the dragged item in the `ListViewItem.Tag` so the original object can be identified and removed after a successful drag.

## Code example

```csharp
this.listView.ItemDrag += ListView_ItemDrag;
this.listView.DragEnter += ListView_DragEnter;
this.listView.DragDrop += listView_DragDrop;
this.sfDataGrid.RowDragDropController.Drop += RowDragDropController_Drop; 

private void ListView_DragEnter(object sender, DragEventArgs e)
{
    e.Effect = DragDropEffects.Move;
}

private void ListView_ItemDrag(object sender, ItemDragEventArgs e)
{
    var item = (ListViewItem)e.Item;
    listView.DoDragDrop(item, DragDropEffects.Move | DragDropEffects.Copy);
}

private void listView_DragDrop(object sender, DragEventArgs e)
{
    // Orders being dropped into the ListView
    var droppedRecords = new List&lt;OrderInfo&gt;();
    var draggedListViewItem = (ListViewItem)e.Data.GetData(typeof(ListViewItem));

    // Detect if the drag source is SfDataGrid
    bool isFromDataGrid = false;
    if (e.Data.GetDataPresent("Records"))
    {
        isFromDataGrid = true;
        var data = e.Data.GetData("Records");
        if (data is OrderInfo order)
            droppedRecords.Add(order);
        else if (data is System.Collections.IEnumerable sequence)
            foreach (var item in sequence) if (item is OrderInfo orderInSequence) droppedRecords.Add(orderInSequence);
    }

    // If dragged from ListView, remove original and include its order
    if (draggedListViewItem != null)
    {
        if (draggedListViewItem.Tag is OrderInfo orderFromTag)
            droppedRecords.Add(orderFromTag);
        listView.Items.Remove(draggedListViewItem);
    }

    // Insert into ListView
    foreach (var order in droppedRecords)
        listView.Items.Add(new ListViewItem(order.ShipCountry ?? string.Empty) { Tag = order });

    // If records came from SfDataGrid, remove them from the grid source
    if (isFromDataGrid)
    {
        var source = sfDataGrid1.View?.SourceCollection as IList;
        if (source != null)
            foreach (var order in droppedRecords)
                if (source.Contains(order)) source.Remove(order);
    }
}

private void RowDragDropController_Drop(object sender, GridRowDropEventArgs e)
{
    if (e.IsFromOutsideSource)
    {
        var draggedOrders = new List&lt;OrderInfo&gt;();

        if (e.Data is IDataObject dataObject)
        {
            if (dataObject.GetDataPresent(typeof(ListViewItem)))
            {
                var listViewItems = dataObject.GetData(typeof(ListViewItem)) as ListViewItem;
                if (listViewItems?.Tag is OrderInfo orderFromTag)
                    draggedOrders.Add(orderFromTag);
            }
        }
        var collection = sfDataGrid1.View?.SourceCollection as IList;

        int targetRowIndex = sfDataGrid1.TableControl.ResolveToRowIndex(e.TargetRecord as OrderInfo);

        int recordIndex = sfDataGrid1.TableControl.ResolveToRecordIndex(targetRowIndex);

        int insertIndex = (e.DropPosition == DropPosition.DropAbove)? recordIndex + 1 : recordIndex;

        foreach (var order in draggedOrders)
        {
            collection.Insert(insertIndex, order);
            insertIndex++; 
        }

        foreach (var order in draggedOrders)
        {
            var toRemove = listView1.Items.Cast&lt;ListViewItem&gt;().FirstOrDefault(i =&gt; ReferenceEquals(i.Tag, order));
            if (toRemove != null)
                listView1.Items.Remove(toRemove);
        }
    }
}
```

## Sample output
![Drag Drop Between Controls](Assets/DragDropBetweenControls_Image.png)
