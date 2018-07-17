Show all package references of all csporj files. Unique sorted by name and version.

gci -r -filter *.csproj | select-xml -xpath //PackageReference | select-object -ExpandProperty Node | sort-object {$_.Include, $_.Version} -unique
