Once you have built the P compiler following [these steps](https://github.com/p-org/P/wiki), you can now use it in your new sample app by integrating it into your *.vcxproj file.  Follow these steps:

1. Ensure your sample app is under the P folder someplace
2. Add the following to your vcxproj file after the import Microsoft.Cpp.props line:

  `<Import Project="$([MSBuild]::GetDirectoryNameOfFileAbove($(MSBuildThisFileDirectory), p.props))\p.props" />`  
  `<Import Project="$([MSBuild]::GetDirectoryNameOfFileAbove($(MSBuildThisFileDirectory), p.props))\Bld\Targets\p.targets" />`

3. Add your sample.p file to the project like this:

  `<ItemGroup>`  
  `  <PCompile Include="sample.p" />`  
  `</ItemGroup>` 

This will cause the file to be compiled with the pc.exe compiler.  The outputs "program.h, program.c and stubs.c" will be placed in your project folder.  You can then include program.h and program.c in your project.  Copy the stubs.c file and implement it in your main.c file, but do not edit stubs.c because it will get regenerated every time you edit sample.p and then you will lose your edits.
