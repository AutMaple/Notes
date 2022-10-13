# # 问题

## 8.0 版本无法使用 Attribute 的问题

解决方法：在 <项目名>.csproj 文件中添加如下代码即可

```xml
<Target Name="FixWaty_RemoveDuplicateAnalyzers" BeforeTargets="CoreCompile">
    <ItemGroup>
        <FilteredAnalyzer Include="@(Analyzer->Distinct())" />
        <Analyzer Remove="@(Analyzer)" />
        <Analyzer Include="@(FilteredAnalyzer)" />
    </ItemGroup>
</Target>
```

# RelayCommandAttribute 的用法

- [RelayCommandAttribute](https://learn.microsoft.com/en-us/dotnet/communitytoolkit/mvvm/generators/relaycommand)

# 参考文章

- [8.0 特性](https://devblogs.microsoft.com/dotnet/announcing-the-dotnet-community-toolkit-800/)

- [官方文档](https://learn.microsoft.com/en-us/dotnet/communitytoolkit/mvvm/)