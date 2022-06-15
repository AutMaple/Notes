# 获取选中的行中的数据

```csharp
private void buttonAdd_Click(object sender, EventArgs e)
{
    // int selectRowCount = userDataView.SelectedRows.Count;
    int index = userDataView.SelectedRows[0].Index;
    DataGridViewRow row =  userDataView.Rows.SharedRow(index);
    // 获取每个单元格中的信息
    foreach(DataGridViewCell cell in row.Cells)
    {
        Console.WriteLine(cell.Value.ToString());
    }
}

```

