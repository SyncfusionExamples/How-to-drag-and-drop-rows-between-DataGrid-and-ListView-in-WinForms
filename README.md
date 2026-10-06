# Drag and drop rows between Data Grid and List View in WinForms

This sample demonstrates how to drag and drop rows between a Syncfusion `WinForms Data Grid` and a Windows Forms `List View` control.

The sample handles the drag-and-drop interaction using the `GridRowDragDropController.Drop` event from the Data Grid and the `ListView.ItemDrag`, `DragEnter`, and `DragDrop` events from the ListView.

## Features

- Drag rows from `Data Grid` to `List View`
- Drag rows from `List View` to `Data Grid`
- Keep the bound data source synchronized while moving records
- Insert records above or below the target row in the Data Grid

## Screenshot

![Drag Drop Between Controls](Assets/DragDropBetweenControls_Image.png)

## Requirements

- Visual Studio 2015 or later
- .NET Framework 4.6.2 or later
- Syncfusion WinForms Data Grid package

## Project structure

- `DragDropBetweenControls/` - Main sample application
- `Assets/` - Screenshot image used in this sample

## How to run this sample

1. Open the `DragDropBetweenControls.sln` file in Visual Studio.
2. Restore NuGet packages.
3. Build the solution.
4. Run the application.

## Related links

- [WinForms DataGrid documentation](https://help.syncfusion.com/windowsforms/datagrid/overview)
- [GridRowDragDropController.Drop API reference](https://help.syncfusion.com/cr/windowsforms/Syncfusion.WinForms.DataGrid.Interactivity.RowDragDropController.html#Syncfusion_WinForms_DataGrid_Interactivity_RowDragDropController_Drop)
