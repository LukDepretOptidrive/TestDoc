##Welcome to Topic 1

Try an editor

 1. kflkfgdlm
 2. gfdklmfk
 3. ffgkdl

~~fgdgdfgfdgdffgd~~

|fhgf|  hgf|vcvc|
|--|--|--|
| cvcv |vcvcxcvc  |vcvcxcvxc

 - [ ] todo1
 - [ ] task2
 - [x] etc


> dvcxvcxv
> cv
> cxv
> cx
> v
> c



Importing 3D objects into Avalon from 3DS files

![](file:///C:/Users/luk.depret/AppData/Local/Packages/Microsoft.Office.OneNote_8wekyb3d8bbwe/TempState/msohtmlclip/clip_image001.jpg)

[Andrej Benedik](https://www.codeproject.com/script/Membership/View.aspx?mid=1554223), 7 Jul 2005

![](file:///C:/Users/luk.depret/AppData/Local/Packages/Microsoft.Office.OneNote_8wekyb3d8bbwe/TempState/msohtmlclip/clip_image002.png)

![](file:///C:/Users/luk.depret/AppData/Local/Packages/Microsoft.Office.OneNote_8wekyb3d8bbwe/TempState/msohtmlclip/clip_image003.png)

4.88 (18 votes)

Rate this:

[vote 1vote 2vote 3vote 4vote 5](https://www.codeproject.com/Articles/10221/Importing-3D-objects-into-Avalon-from-3DS-files#)

Class library for importing 3D objects from 3DS files into Avalon, and a simple 3D object viewer.

-   [Download source solution - 71.0 KB](https://www.codeproject.com/KB/graphics/avalon3ds/Avalon3ds_src.zip)

Contents

-   [Contents](https://www.codeproject.com/Articles/10221/Importing-3D-objects-into-Avalon-from-3DS-files#Contents0)
-   [Introduction](https://www.codeproject.com/Articles/10221/Importing-3D-objects-into-Avalon-from-3DS-files#Introduction1)
-   [Importing 3D objects from 3DS files](https://www.codeproject.com/Articles/10221/Importing-3D-objects-into-Avalon-from-3DS-files#Importing3Dobjectsfrom3dsfiles2)

-   [Mesh Definition Problem](https://www.codeproject.com/Articles/10221/Importing-3D-objects-into-Avalon-from-3DS-files#MeshDefinitionProblem3)
-   [Defining Solution](https://www.codeproject.com/Articles/10221/Importing-3D-objects-into-Avalon-from-3DS-files#DefiningSolution4)
-   [MeshUtilities project](https://www.codeproject.com/Articles/10221/Importing-3D-objects-into-Avalon-from-3DS-files#MeshUtilitiesproject5)

-   [Viewer3D](https://www.codeproject.com/Articles/10221/Importing-3D-objects-into-Avalon-from-3DS-files#Viewer3D6)

-   [Viewer3D "user's guide"](https://www.codeproject.com/Articles/10221/Importing-3D-objects-into-Avalon-from-3DS-files#Viewer3Dusersguide7)
-   [Behind the Viewer3D](https://www.codeproject.com/Articles/10221/Importing-3D-objects-into-Avalon-from-3DS-files#BehindtheViewer3d8)

-   [Conclusion](https://www.codeproject.com/Articles/10221/Importing-3D-objects-into-Avalon-from-3DS-files#Conclusion9)

Introduction

When Microsoft published Avalon as Community Technology Preview, I became very interested in its 3D functionality. I believe that 3D is a natural step in evolution of displays. The evolution went from teleprinters to black & white monitors (and black & green and "better" black & orange). Then came the first color monitors. And now their time is almost over - LCD monitors will soon take their place. 10 years ago, it was hard even to imagine that a 1 cm deep monitor could show such a picture. And the evolution is of course not over. The first 3D monitors are already on the market.

More about 3D display technology can be found [here](http://www.sharp3d.com/technology/). OK, the technology still have lots of limitations, but this is just the beginning. Recently I read [an article](http://www.mobileburn.com/gallery.jsp?Id=599) that Sharp has released a mobile telephone with a 3D screen and that it looks great.

I believe that in the future, all the icons, buttons and windows in OSs will be three dimensional. And we will also see them in 3D. For me, Avalon is the beginning of such a world.

In this article, I will firstly focus on importing 3D objects from a 3DS file. In the second part, I will present a sample Avalon application where a user can view objects from 3DS files and export them as XAML text.

System Requirements

-   Avalon RCL
-   Visual Studio 2005 beta 2

Importing 3D objects from 3DS files

Mesh Definition Problem

Let me just quickly refresh the basics in computer 3D graphics. 3D objects in computer graphics consists of many faces (TriangleIndices in Avalon). Each face if defined with three vertexes (Positions). Each vertex can also have a normal defined. Normal is a 3D vector that is used to shade the object.

For more information about 3D computer graphics, I recommend the following links:

-   ["Face and Vertex Normal Vectors" from MSDN](http://msdn.microsoft.com/library/default.asp?url=/library/en-us/directx9_c/directx/graphics/programmingguide/GettingStarted/3DCoordinateSystems/facevertexnormalvectors.asp)
-   ["3D computer graphics" from Answers.com](http://www.answers.com/main/ntquery?method=4&dsid=2222&dekey=3D+computer+graphics&gwp=8&curtab=2222_1)

There are two ways to define 3D objects in Avalon. They can be defined in code-behind or in XAML within <Model3Dgroup>. The definition in code-behind is usually used for objects that can be mathematically defined, like sphere or torus. In this case, all the points, triangle indices and normals can be calculated using mathematical formulas.

In XAML object definition, all the data are written in text format. The following code represents the definition for a simple tetrahedron:

Hide Copy Code

<GeometryModel3D.Geometry>  
<MeshGeometry3D

TriangleIndices="0 1 2  1 2 3  2 3 0  0 1 3"

Normals="-1,-1,0 1,-1,0 1,0,0 0,0,1"

Positions="-2,-2,-2  2,-2,-2  0,2,-2  0,0,1"/>  
</GeometryModel3D.Geometry>

As you can see, even for very simple 3D objects, the XAML definition is quite complicated. Note that the above definition is for the simplest 3D object - it has only four corners (Positions) and four triangles (TriangleIndices). For such an object, it is still possible to define the data on your own - with pencil and a sheet of paper. But already for a simple cube this can become a breath-taking job. Furthermore, the common 3D objects can have hundreds of corners and triangles with complicated normals and texture coordinates. So there is almost no way to calculate the data for the 3D objects by ourselves.

Defining Solution

On the other hand, there are some great 3D modeling programs. It would be very convenient to define objects in those programs and import them directly into Avalon.

I found a description of 3DS file format. This file format was used by AutoDesk's 3D Studio program. Because of the great popularity of this program, the file format has become very widely used. Now almost all 3D modeling programs support some kind of import from or export to this file format. Also, there are huge web libraries that are providing objects in this file format (a large list of libraries with free 3D objects can be found [here](http://www.amazing3d.com/modfree.html)).

The idea is to be able to do something like:

Hide Copy Code

Viewport1.Models = reader3ds.ReadFile("sample_scene.3ds");

The reading part of the solution is implemented in the MeshUtilites project. The main class there is Reader3ds which actually reads the 3DS file. There is also ChunkIds class that contains constants used for reading a 3DS file. After I wrote Reader3ds, I discovered that it would be very useful if it would be possible to convert read 3D objects into flat or gouraud shaded objects. This is done in MeshFactory class. And finally there is also XamlExport class that is used to export objects (actually the whole Viewport3D) into XML text.

MeshUtilities project

Reader3ds and ChunkIds

The Reader3ds class is used to read data from a 3DS file. To do this we must firstly know the structure of the 3DS file format.

The data in a 3DS file is divided in so called "chunks". Each chunk has its ID, a two byte value describing the type of the chunk. For example, 0x4110 in chunk's ID means that the chunk contains vertex information (Positions for Avalon). After the ID comes the length (as long) of the chunk. This means that if the chunk is unknown or not important, it can be skipped. After the length comes the actual data. The format of the data depends on the type of the chunk. For example, vertex data starts with the number of vertexes followed by 3 * vertex_count float numbers representing x, y and z coordinates of each vertex.

The 3DS file contains all the data to represent a quite complex 3D world. Apart from vertexes and triangles, it contains also information about materials, cameras, lights and also data about animation.

The current implementation of Reader3ds reads Positions, TriangleIndices, texture coordinates and also cameras and lights. It does not read material and animation data.

To read a 3DS file, a Reader3ds class must be instantiated. Then it is possible to call its ReadFile method with file name as parameter. The method returns Model3Dgroup object that can be set to Viewport3D's Models collection.

Because the materials are not imported it is possible to set the default material that will be used for all objects - this can be done by setting DefaultMaterial property. If this property is not set, a solid silver color brush material is used as default.

Many 3DS files do not contain a defined light. In this case, a default light is added to Model3Dgroup (Direction = 0, 0, -1), so the scene does not look completely dark when it is rendered. This can also be disabled by setting AddDefaultLight to false.

The following code firstly sets the default material to gold and than reads a "coins.3ds" file. Then the objects are added to Viewport1.Models. Finally the first five objects (coins) are set to be silver.

Hide Copy Code

Reader3ds coinsReader = new Reader3ds();  
coinsReader.DefaultMaterial =  
new DiffuseMaterial(new SolidColorBrush(Colors.Gold));  
Viewport1.Models = coinsReader.ReadFile("coins.3ds");  
for (int i=0; i<5; i++)  
((GeometryModel3D)Viewport1.Models.Children[i]).Material =  
new DiffuseMaterial(new SolidColorBrush(Colors.Silver));

MeshFactory

Avalon is using Gouraud shading to render objects. This means that the objects are smoothed, there are no definite borders between faces so the surfaces do not appear flat. The objects that do not have normals specified are rendered using Gouraud shading. For most cases this is okay, but sometimes we need flat surfaces - for example, for a cube or a pyramid.

In those cases, we need to set the vertex normals to point perpendicular to the surface. All three vertexes that form the face should have normals that are pointing away from the front side of the face. In this case, face will be shaded with only one color - there will be no color gradients from one vertex to another.

Because objects are composed of triangular faces, each vertex is used in three neighbouring faces. One vertex can have only one normal. This is okay for Gouraud shading where the normal is pointing in an "average direction" for all three faces. But for flat surfaces, all normals of one face should point to the same direction. Unfortunately, this means that for flat objects all vertexes must be tripled. So each point in 3D space must be represented with three vertexes - just because of different normals.

At first, I hoped that simply reading Positions and TriangleIndices from a 3DS file would be enough to represent 3D objects in Avalon. For many objects, this is enough. But flat objects do require additional normals. Also, some objects imported from 3DS are not rendered properly - they look like there are some tensions along some edges. This happens because some vertexes are doubled - there is more than one vertex defined for one point in space. In this case, the rendering engine does not compute the normals for shading correctly.

![Shading examples](file:///C:/Users/luk.depret/AppData/Local/Packages/Microsoft.Office.OneNote_8wekyb3d8bbwe/TempState/msohtmlclip/clip_image004.png)

The above image represents the same object in three different cases. The first is an object without normals defined. The default Gouraud shading was applied, but there are some anomalies visible - the number of vertexes is incorrect. The second image is rendered properly using Gouraud shading because the very same vertexes were combined. The third image shows the same object but with specified normals and tripled vertexes that make the object look flat.

The purpose of MeshFactory is to convert the original meshes into gouraud of flat shaded mesh. All methods in MeshFactory are static so usage is simple:

Hide Copy Code

MeshGeometry3D one3dObject;  
Model3DGroup whole3dScene;

// convert only one MeshGeometry3D  
one3dObject = MeshUtilities.MeshFactory.GetGouraudShadedMesh(original3dObject);  
one3dObject = MeshUtilities.MeshFactory.GetFlatShadedMesh(original3dObject);

// convert all objects in Model3DGroup  
whole3dScene = MeshUtilities.MeshFactory.GetGouraudModelGroup(originalScene);  
whole3dScene = MeshUtilities.MeshFactory.GetFlatModelGroup(originalScene);

XamlExport

XamlExport class, as its name suggests, converts the whole Model3DGroup with all 3D objects, lights and the camera into a XAML text that represents the same scene. The output XAML can than be previewed in XamlPad or directly used in a XAML file.

Using XamlExport:

Hide Copy Code

MeshUtilities.XamlExporter thisXamlExporter = new XamlExporter();

thisXamlExporter.DefaultMaterialXaml =  
"<GeometryModel3D.Material><DiffuseMaterial> ...";  
thisXamlExporter.DoubleFormatString = "#0.###";  
thisXamlExporter.LoadXamlTemplate("XamlExportDefaultTeplate.xaml");

string xamlText = thisXamlExporter.ExportModel3DGroup(Viewport1.Models,  
Viewport1.Camera);

Firstly, we have to create an instance of XamlExport class. Then we can (but it is not necessary) define the default material XAML string and DoubleFormatString (used to format all double values). We must also define XamlTemplate. This can be done by loading it from a file (as done above) or we can set the XamlTemplate property with template text. Now we can call ExportModel3DGroup method and pass Model3DGroup and Camera (can also be null) as parameters. The method returns a string that represents the whole 3D scene in XAML.

The XamlExport uses a template to define the proper XAML structure - that is where 3D meshes, lights and camera are inserted. If you open the XamlExportDefaultTeplate.xaml (also enclosed), you will notice that there are three comments added: "<!-- IMPORT_MESH_RESOURCES -->", "<!-- IMPORT_CAMERA -->" and "<!-- IMPORT_CHILDREN -->". In ExportModel3DGroup method, the comments are replaced with appropriate data. If you define your own templates you should just put those three comments into it.

I would recommend using exported XAML for simple scenes, for more complex ones it would be probably better to read 3DS file in code-behind by using Reader3ds.

Viewer3D

![Viewer3D](file:///C:/Users/luk.depret/AppData/Local/Packages/Microsoft.Office.OneNote_8wekyb3d8bbwe/TempState/msohtmlclip/clip_image005.png)

Before I describe the Viewer3D, I would like to congratulate Microsoft on the possibilities Avalon brings. I have a lot of experience in programming web and Windows interfaces and I find Avalon a big step forward - in the simplicity of defining UI and also in power and the unlimited possibilities of it. It is very simple to define very complex and great looking user interfaces that would be almost impossible to define in other technologies.

The above picture with 3D background, rounded and semi-transparent panels show just some of the "cookies" Avalon brings. Viewer3D also uses some more advanced features - triggers, animation, ... I will describe some of them later. Firstly let me describe the functionality of Viewer3D.

Viewer3D "user's guide"

As you can see from the above image, the most space in the window is occupied with 3D objects from a 3DS file (a house and a man). On the left side, there are various panels that control the output or do some other actions. On the bottom, there are four buttons that show or hide the panels. Right from the buttons, there is a textbox where a file name can be written. And in the bottom right there is a button to load the specified file.

The first panel represents the current shading model that is used for the 3D objects. If Original radio button is selected then the objects are as they are read from the 3DS file. If Flat or Gouraud button is selected, the objects are transformed using the appropriate shading technique - look MeshFactory for more details. There is also a "Show wireframe" check box - when checked a wireframe (lots of ScreenSpaceLines3D) is added to the scene. On slower machines, this operation can take some time - so please do not throw the poor mouse onto the wall if the action is not immediate. This function can be very useful to inspect the complexity of the models.

The next panel is pretty simple. It has a textbox to enter the number of decimals and a button to copy the whole scene as XAML into clipboard. Use fewer decimals (2 or 3) for smaller XAML and more decimals for more accurate output (and also for small objects). As I described before, a XAML template is used to create the XAML string. The Viewer3d uses the enclosed XamlExportDefaultTeplate.xaml template. This template can be changed to produce different outputs.

Then there is the lights panel. There are shown all the lights that were read from the 3DS file. A free light is also added - if there is no light defined in a 3DS file, the free light is the default one. You can turn each light on or off. If the free light is turned on, its direction can be set with two sliders below the check box. The color of the light is also imported from the 3DS file. The above scene is actually rendered using the default silver material, but the imported light gives it a yellowish tone. The free light shines in white color.

The last panel represents the cameras. There are all the cameras that were imported and also a free camera that is always available. Because only one camera can be active, the camera selection is done using radio buttons. If the free camera is selected, the user can set its direction, rotation and distance from the central point (at coordinates 0, 0, 0). There is also a text box where the maximum distance can be set - this is the distance when the last slider is in its most right position. If the user goes with the mouse over the camera's panel, a small camera preview panel is shown on the right. This panel can be useful to see from which direction the camera is "looking" at the object.

It should be possible to load most 3DS files. Unfortunately, it is not possible to load material information and also more complex objects can be strange looking, but for simpler objects Viewer3d can be quite useful (hopefully :)

Behind the Viewer3d

The Viewer3d consist of 600 and something lines of C# code. This code is really nothing special. The more interesting part is the XAML. There the visual appearance is defined. I will not write about how I defined the controls and how they are positioned to the window. If you are a beginner, I would recommend reading WinFX SDK documentation. I would like to point out some more interesting and advanced features that were used in Viewer3d.

Buttons style

There are five buttons on the bottom of Viewer3d window. If you move a mouse pointer over each button it becomes brighter. It is very simple to create such a functionality in Avalon. The following code defines the style that is used for the buttons:

Hide Copy Code

<Style x:Key="SimpleButton" TargetType="{x:Type Button}">  
<Setter Property="Background" Value="VerticalGradient #ddd #888"/>  
<Setter Property ="VerticalAlignment" Value="Center"/>  
<Style.Triggers>  
<Trigger Property ="Button.IsMouseOver" Value="True">  
<Setter Property="Background" Value="VerticalGradient #fff #aaa"/>  
</Trigger>  
</Style.Triggers>  
</Style>

The upper code defines a SimpleButton style with two properties set: Background and VerticalAlignment. It also defines a property trigger that changes the Background property when the mouse is over the button. This is just a very simple example. In this manner it is possible to define very complex styles. And the best is that such functionality is defined without any use of code. That means that it is possible to define styles in separate resource files and then just simply replacing it with completely different style definitions which can give the application an entirely different outlook.

Error message

Secondly, I would like to present the way Viewer3d displays error messages that occur during loading of 3DS files. The image below shows a sample error message. It is displayed in the middle of the window over the previous 3D objects. Of course it could be much better - especially the error text which is now truncated. But it is better than nothing. And this is not just a simple error message. If you enter a wrong file name in Viewer3d, you will see its more interesting side.

![Error message](file:///C:/Users/luk.depret/AppData/Local/Packages/Microsoft.Office.OneNote_8wekyb3d8bbwe/TempState/msohtmlclip/clip_image006.gif)

You will see that after 4 seconds, the whole message will start to fade away. This is done in the catch block that follows the loading of the 3DS file:

Hide Copy Code

this.FindStoryboardClock(errorDisappearing).ClockController.Begin();

The above statement simply starts an errorDisappearing animation. The animation is defined in XAML:

Hide Copy Code

<Window.Storyboards>  
<ParallelTimeline x:Name="errorDisappearing">  
<SetterTimeline TargetName="errorGrid" Path="(Grid.Opacity)">  
<DoubleAnimation To="1" Duration="0:0:0.5"/>  
<DoubleAnimation To="0" Duration="0:0:2" BeginTime="0:0:4"/>  
</SetterTimeline>  
</ParallelTimeline>  
</Window.Storyboards>

The animation is defined in the Window.Storyboards element. It changes the Grid.Opacity property of errorGrid. In the first half second, it changes the current value of Opacity to 1 - it shows the errorGrid if it was hidden by prior animation. 4 seconds from the beginning of the animation it starts to change the opacity to 0 (hides errorGrid). It takes two seconds to hide the grid.

Camera preview panel

The last and the most complex UI element of Viewer3d is the camera preview panel. It is shown on the right of the Camera panel and above the bottom buttons. But most of the time it is hidden. It is shown only when the mouse pointer is in the Camera panel. It shows a 3D camera that is rotating around a golden sphere and shows from where the camera is looking at the loaded 3D objects.

The preview panel is defined similarly as the other panels. But instead of buttons, textboxes and other controls, it contains a Viewport3D object. Except a camera there are no 3D objects defined. They are added in the InitPreview method. Firstly, camera and a sphere are loaded from a 3DS file. Then the materials are set - light gray for the camera and gold for the sphere. Then two rotate transform objects are added to the scene. They are used to rotate the camera around the sphere. After the camera is moved either by moving free camera or by changing selected camera, the new rotation angle is calculated (in SelectedCameraChanged) and the preview camera is rotated to show the new camera position.

This is all done in the code-behind. But there is also a XAMl part of the panel functionality that defines showing and hiding of the panel:

Hide Copy Code

<Window.Triggers>  
<EventTrigger RoutedEvent="Panel.MouseEnter" SourceName="CamerasPanel">  
<EventTrigger.Actions>  
<StopAction TargetName="hidePositionPreviewPanel" />  
<BeginAction TargetName="showPositionPreviewPanel" />  
</EventTrigger.Actions>  
</EventTrigger>  
<EventTrigger RoutedEvent="Panel.MouseLeave" SourceName="CamerasPanel">  
<EventTrigger.Actions>  
<StopAction TargetName="showPositionPreviewPanel" />  
<BeginAction TargetName="hidePositionPreviewPanel" />  
</EventTrigger.Actions>  
</EventTrigger>  
</Window.Triggers>

<Window.Storyboards>  
<ParallelTimeline x:Name="showPositionPreviewPanel">  
<SetterTimeline TargetName="PositionPreviewPanel" Path="(Grid.Opacity)">  
<DoubleAnimation From="0" To="1" Duration="0:0:0.5" />  
</SetterTimeline>  
</ParallelTimeline>  
<ParallelTimeline x:Name="hidePositionPreviewPanel">  
<SetterTimeline TargetName="PositionPreviewPanel" Path="(Grid.Opacity)">  
<DoubleAnimation From="1" To="0" Duration="0:0:0.5"/>  
</SetterTimeline>  
</ParallelTimeline>  
</Window.Storyboards>

Firstly, there are two event triggers defined. The first one is started when the mouse pointer enters the CamerasPanel. The other is started when the mouse leaves the panel. A closer look reveals that the code actually starts and ends two actions: hidePositionPreviewPanel and showPositionPreviewPanel. The actions are defined below event triggers. They are simple storyboards that by changing the opacity show or hide PositionPreviewPanel.

Conclusion

I hope you will find the application useful - so for loading 3D objects from 3DS files and also as a sample Avalon application with some Avalon cookies. I find loading 3D objects directly from files very convenient and maybe it would not be a bad idea if such functionality would be a part of Avalon's object model.

I must say that I am very satisfied with Microsoft's work. They have really taken the good parts from both Windows and web worlds and put them into Avalon. I am hardly waiting the release of Longhorn and the new application designed for Avalon.

Finally I would like to encourage you to post your demos or samples that use Reader3ds, to Avalon's web community.

License

This article has no explicit license attached to it but may contain usage terms in the article text or the download files themselves. If in doubt please contact the author via the discussion board below.

A list of licenses authors might use can be found [here](https://www.codeproject.com/info/Licenses.aspx)

Share

-   [twitter](http://twitter.com/home?status=Importing+3D+objects+into+Avalon+from+3DS+files+-+CodeProject+-+https%3a%2f%2fwww.codeproject.com%2fArticles%2f10221%2fImporting-3D-objects-into-Avalon-from-3DS-files)

About the Author

![](file:///C:/Users/luk.depret/AppData/Local/Packages/Microsoft.Office.OneNote_8wekyb3d8bbwe/TempState/msohtmlclip/clip_image001.jpg)

[Andrej Benedik](https://www.codeproject.com/Members/Andrej-Benedik)

Web Developer

Slovenia

![Slovenia](file:///C:/Users/luk.depret/AppData/Local/Packages/Microsoft.Office.OneNote_8wekyb3d8bbwe/TempState/msohtmlclip/clip_image007.gif)

I am Microsoft MCP - Implementing Web Applications with Microsoft C# .NET and am working for a small Slovenian software developing company DSolutions SI. My free time is ususally filled with sport activities (cycling, running, wind-surfing, mountaineering, cross country skiing). But I also find some time to play with new technologies.

Van <[https://www.codeproject.com/Articles/10221/Importing-3D-objects-into-Avalon-from-3DS-files](https://www.codeproject.com/Articles/10221/Importing-3D-objects-into-Avalon-from-3DS-files)>
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE0MzEyMjI0MDRdfQ==
-->