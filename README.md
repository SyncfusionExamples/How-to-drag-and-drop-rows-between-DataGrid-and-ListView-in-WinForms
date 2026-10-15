# Drag and drop rows between Data Grid and List View in WinForms 

Overview
--------
This sample shows how to implement row drag-and-drop between a WinForms ListView and Syncfusion's SfDataGrid using the GridRowDragDropController.Drop handler. It demonstrates dragging from the data grid into the ListView and from the ListView back into the data grid, including removing and inserting records in the underlying collections.

Prerequisites
-------------
- .NET Framework / .NET compatible with the Syncfusion WinForms DataGrid control.
- Syncfusion WinForms NuGet packages (SfDataGrid).
- A WinForms project with a ListView and SfDataGrid on a form.

How it works
------------
- The ListView raises ItemDrag, DragEnter and DragDrop events to start and handle drag operations.
- The SfDataGrid uses RowDragDropController.Drop to accept drops from outside sources and to insert records into its collection.
- When items move between controls, the underlying collection is updated (removed from source, inserted into destination).

To perform dragging between the ListView and SfDataGrid, by using the [GridRowDragDropController.Drop](https://help.syncfusion.com/cr/windowsforms/Syncfusion.WinForms.DataGrid.Interactivity.RowDragDro[...]

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
