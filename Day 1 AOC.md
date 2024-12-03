
## Part 1

Left Query:

    let
        Source = Csv.Document(File.Contents("C:\Users\JCBE\Downloads\day01.txt"),2,"",ExtraValues.Ignore,1252),
        #"Renamed Columns" = Table.RenameColumns(Source,{{"Column1", "Left"}, {"Column2", "Right"}}),
        #"Removed Columns" = Table.RemoveColumns(#"Renamed Columns",{"Right"}),
        #"Sorted Rows" = Table.Sort(#"Removed Columns",{{"Left", Order.Ascending}}),
        #"Renamed Columns1" = Table.RenameColumns(#"Sorted Rows",{{"Left", "Left_Sorted"}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Renamed Columns1",{{"Left_Sorted", Int64.Type}}),
        #"Added Index" = Table.AddIndexColumn(#"Changed Type", "Index", 0, 1, Int64.Type),
        #"Reordered Columns" = Table.ReorderColumns(#"Added Index",{"Index", "Left_Sorted"}),
        #"Merged Queries" = Table.NestedJoin(#"Reordered Columns", {"Index"}, Right, {"Index"}, "Right", JoinKind.LeftOuter),
        #"Expanded Right" = Table.ExpandTableColumn(#"Merged Queries", "Right", {"Right_Sorted"}, {"Right_Sorted"}),
        #"Added Custom" = Table.AddColumn(#"Expanded Right", "Custom", each Number.Abs([Left_Sorted]-[Right_Sorted])),
        #"Calculated Sum" = List.Sum(#"Added Custom"[Custom]),
        Custom1 = List.Sum(Table.Column(#"Calculated Sum", "Value"))
    in
        Custom1

Right Query:

    let
        Source = Csv.Document(File.Contents("C:\Users\JCBE\Downloads\day01.txt"),2,"",ExtraValues.Ignore,1252),
        #"Renamed Columns" = Table.RenameColumns(Source,{{"Column1", "Left"}, {"Column2", "Right"}}),
        Right1 = #"Renamed Columns"[Right],
        #"Sorted Items" = List.Sort(Right1,Order.Ascending),
        #"Converted to Table" = Table.FromList(#"Sorted Items", Splitter.SplitByNothing(), null, null, ExtraValues.Error),
        #"Renamed Columns1" = Table.RenameColumns(#"Converted to Table",{{"Column1", "Right_Sorted"}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Renamed Columns1",{{"Right_Sorted", Int64.Type}}),
        #"Added Index" = Table.AddIndexColumn(#"Changed Type", "Index", 0, 1, Int64.Type),
        #"Reordered Columns" = Table.ReorderColumns(#"Added Index",{"Index", "Right_Sorted"})
    in
        #"Reordered Columns"

## Part 2

Right Query:

    let
        Source = Csv.Document(File.Contents("C:\Users\JCBE\Downloads\day01.txt"),2,"",ExtraValues.Ignore,1252),
        #"Renamed Columns" = Table.RenameColumns(Source,{{"Column1", "Left"}, {"Column2", "Right"}}),
        Right1 = #"Renamed Columns"[Right],
        #"Sorted Items" = List.Sort(Right1,Order.Ascending),
        #"Converted to Table" = Table.FromList(#"Sorted Items", Splitter.SplitByNothing(), null, null, ExtraValues.Error),
        #"Renamed Columns1" = Table.RenameColumns(#"Converted to Table",{{"Column1", "Right_Sorted"}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Renamed Columns1",{{"Right_Sorted", Int64.Type}}),
        #"Added Index" = Table.AddIndexColumn(#"Changed Type", "Index", 0, 1, Int64.Type),
        #"Reordered Columns" = Table.ReorderColumns(#"Added Index",{"Index", "Right_Sorted"}),
        #"Removed Columns" = Table.RemoveColumns(#"Reordered Columns",{"Index"}),
        #"Grouped Rows" = Table.Group(#"Removed Columns", {"Right_Sorted"}, {{"Count", each Table.RowCount(_), Int64.Type}}),
        #"Filtered Rows" = Table.SelectRows(#"Grouped Rows", each true),
        #"Added Custom" = Table.AddColumn(#"Filtered Rows", "RightTimesCount", each Value.Multiply([Right_Sorted], [Count])),
        #"Renamed Columns2" = Table.RenameColumns(#"Added Custom",{{"Count", "Right_Count"}}),
        #"Added Index1" = Table.AddIndexColumn(#"Renamed Columns2", "Index", 0, 1, Int64.Type),
        #"Reordered Columns1" = Table.ReorderColumns(#"Added Index1",{"Index", "Right_Sorted", "Right_Count", "RightTimesCount"}),
        #"Merged Queries" = Table.NestedJoin(#"Reordered Columns1", {"Right_Sorted"}, Left_Sorted, {"left_Sorted"}, "Left_Sorted", JoinKind.LeftOuter),
        #"Expanded Left_Sorted" = Table.ExpandTableColumn(#"Merged Queries", "Left_Sorted", {"left_Sorted", "Count"}, {"left_Sorted.1", "Count"}),
        #"Added Custom1" = Table.AddColumn(#"Expanded Left_Sorted", "Custom", each Value.Multiply([RightTimesCount], [Count])),
        Custom = #"Added Custom1"[Custom],
        #"Calculated Sum" = List.Sum(Custom)
    in
        #"Calculated Sum"

Left Query:

    let
        Source = Csv.Document(File.Contents("C:\Users\JCBE\Downloads\day01.txt"),2,"",ExtraValues.Ignore,1252),
        #"Renamed Columns" = Table.RenameColumns(Source,{{"Column1", "Left"}, {"Column2", "Right"}}),
        #"Removed Columns" = Table.RemoveColumns(#"Renamed Columns",{"Right"}),
        #"Sorted Rows" = Table.Sort(#"Removed Columns",{{"Left", Order.Ascending}}),
        #"Renamed Columns1" = Table.RenameColumns(#"Sorted Rows",{{"Left", "Left_Sorted"}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Renamed Columns1",{{"Left_Sorted", Int64.Type}}),
        #"Added Index" = Table.AddIndexColumn(#"Changed Type", "Index", 0, 1, Int64.Type),
        Left_Sorted1 = #"Added Index"[Left_Sorted],
        #"Converted to Table" = Table.FromList(Left_Sorted1, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
        #"Renamed Columns2" = Table.RenameColumns(#"Converted to Table",{{"Column1", "left_Sorted"}}),
        #"Grouped Rows" = Table.Group(#"Renamed Columns2", {"left_Sorted"}, {{"Count", each Table.RowCount(_), Int64.Type}}),
        #"Added Index1" = Table.AddIndexColumn(#"Grouped Rows", "Index", 0, 1, Int64.Type),
        #"Reordered Columns" = Table.ReorderColumns(#"Added Index1",{"Index", "left_Sorted", "Count"})
    in
        #"Reordered Columns"
