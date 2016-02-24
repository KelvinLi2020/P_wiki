Once you have built the P compiler following [these steps](https://github.com/p-org/P/wiki), you can now use it in your new sample app by integrating it into your *.vcxproj file.  Follow these steps:

1. Add the following to your vcxproj file after the import Microsoft.Cpp.props line, be sure to give the correct path to your P git workspace.
 
  `<Import Project="_c:\git\P_\Bld\Targets\p.targets" />`

3. Add your sample.p file to the project like this:

  `<ItemGroup>`  
  `  <PCompile Include="sample.p" />`  
  `</ItemGroup>` 

This will cause the file to be compiled with the pc.exe compiler.  The outputs "program.h, program.c and stubs.c" will be placed in your project folder.  You can then include program.h and program.c in your project.  Copy the stubs.c file and implement it in your main.c file, but do not edit stubs.c because it will get regenerated every time you edit sample.p and then you will lose your edits.

If you run into trouble, see the samples under ~\P\Src\PrtDist\Samples.  These samples use the above trick already.

**Note**: if the p file is not being rebuilt when you expect it to, try using "rebuild" of the project.