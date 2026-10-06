# Drag and drop rows between DataGrid and ListView in WinForms

This sample demonstrates how to drag and drop rows between a Syncfusion WinForms DataGrid (`SfDataGrid`) and a Windows Forms `ListView` control.

The sample handles the drag-and-drop interaction using the `GridRowDragDropController.Drop` event from the DataGrid and the `ListView.ItemDrag`, `DragEnter`, and `DragDrop` events from the ListView.

## Features

- Drag rows from `SfDataGrid` to `ListView`
- Drag rows from `ListView` to `SfDataGrid`
- Keep the bound data source synchronized while moving records
- Insert records above or below the target row in the DataGrid

## Screenshot

![Drag Drop Between Controls](Assets/DragDropBetweenControls_Image.png)

## Requirements

- Visual Studio 2015 or later
- .NET Framework 4.6.2 or later
- Syncfusion WinForms DataGrid package

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
