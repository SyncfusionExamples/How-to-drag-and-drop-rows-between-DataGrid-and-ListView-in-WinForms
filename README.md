# Row drag and drop between DataGrid and ListView

To perform dragging between the ListView and SfDataGrid, by using the [GridRowDragDropController.Drop](https://help.syncfusion.com/cr/windowsforms/Syncfusion.WinForms.DataGrid.Interactivity.RowDragDropController.html#Syncfusion_WinForms_DataGrid_Interactivity_RowDragDropController_Drop) event and you must set the `AllowDrop` property as true in the `ListView` while doing the drag and drop operation from `SfDataGrid` with `ListView` control.

## Sample

```xaml
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

![Drag Drop Between Controls](Assets/DragDropBetweenControls_Image.png)

## Requirements to run the demo

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) or [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)
* Xamarin add-ons for Visual Studio (available via the Visual Studio installer).

## Troubleshooting

### Path too long exception

If you are facing path too long exception when building this example project, close Visual Studio and rename the repository to short and build the project.

