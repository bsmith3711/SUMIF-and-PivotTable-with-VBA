# SUMIF-and-PivotTable-with-VBA
An Excel workbook containing a few advanced functions.

This Excel workbook was created using Kaggle data about ecommerce sales / engagement data.

I created some advanced functions to analyse the data including a SUMIF sheet, a Pivot Table, as well as a VBA macro to automate the raw data cleanse process.

Below is the VBA code for the "CleanseAndCreateColumns" macro:


Sub CleanseAndCreateColumns()
    Dim ws As Worksheet
    Dim lastRow As Long
    Dim i As Long
    Dim cellValue As Variant
    Dim monthValue As String

    ' Reference the actual name of your raw data sheet
    Set ws = ThisWorkbook.Sheets("Raw Data")

    ' Clear existing "Month" and "Flag" column contents but do not delete the columns
    Application.ScreenUpdating = False ' Disable screen updating to improve performance
    With ws
        ' Check if the "Month" and "Flag" columns exist based on headers and clear contents if they do
        If .Cells(1, 5).Value <> "" Then
            .Range("E2:I" & .Cells(.Rows.Count, "E").End(xlUp).Row).ClearContents
        Else
            ' Add the headers for "Month" and "Flag" columns if they don't exist
            .Cells(1, 5).Value = "Month"
            .Cells(1, 6).Value = "Purchase Flag"
            .Cells(1, 7).Value = "View Flag"
            .Cells(1, 8).Value = "Like Flag"
        End If

        ' Find the last row with data in the "Time stamp" column
        lastRow = .Cells(.Rows.Count, "D").End(xlUp).Row

        ' Loop through each row
        For i = 2 To lastRow
            ' Cleanse the date format and write back to the cell
            cellValue = .Cells(i, 4).Value
            If IsDate(cellValue) Then
                .Cells(i, 4).NumberFormat = "mm/dd/yyyy hh:mm AM/PM"
                ' Extract the month and year and write to the "Month" column
                monthValue = MonthName(Month(cellValue), True) & "-" & Right(Year(cellValue), 2) ' "Jan-23" format
                .Cells(i, 5).Value = monthValue
            End If

            ' Check interaction type and set flag
            Select Case LCase(.Cells(i, 3).Value)
                Case "purchase"
                    .Cells(i, 6).Value = 1
                Case "view"
                    .Cells(i, 7).Value = 1
                Case "like"
                    .Cells(i, 8).Value = 1
                Case Else
                    ' Clear the flags if none of the conditions are met
                    .Cells(i, 6).Value = ""
                    .Cells(i, 7).Value = ""
                    .Cells(i, 8).Value = ""
            End Select
        Next i
    End With

    ' AutoFit the column widths
    ws.Columns("E:I").AutoFit
    Application.ScreenUpdating = True ' Re-enable screen updating
End Sub

 
