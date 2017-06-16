Once you have built the P compiler following [these steps](https://github.com/p-org/P/wiki), you can now use it in your new sample app by integrating it into your *.vcxproj file.  Follow these steps:

1. Add the following to your vcxproj file after the import Microsoft.Cpp.props line, be sure to give the correct path to your P git workspace.
 
  `<Import Project="<c:\git\P>\Bld\Targets\p.targets" />`

3. Add your sample.p file to the project like this:

  `<ItemGroup>`  
  `  <PCompile Include="sample.p" />`  
  `</ItemGroup>` 

This will cause the file to be compiled with the pc.exe compiler.  The outputs "sample.h, sample.c, sample.4ml, linker.c and linker.h" will be placed in the "PGenerated" subdirectory of your project folder.  These files will automatically be included in your project.  If you have some model functions you get a stubs.c file also that will show you what you need to implement.  Implement these in a different file (do not edit stubs.c otherwise next time you change your *.p file you will lose your edits to stubs.c).

If you run into trouble, see the samples under ~\P\Src\Samples.  These samples use the above p.targets already.
