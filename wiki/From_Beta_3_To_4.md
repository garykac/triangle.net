# From Beta 3 To 4

Projects compiled against Beta 3 will no longer complile with Beta 4. There are a few breaking changes which will be explained below:

## 1. InputGeometry replaced by IPolygon interface
The new _IPolygon_ interface allows direct access to Points, Segments, Holes and Regions, thus methods like _AddPoint_ or _AddSegment_ from the _InputGeometry_ class are no longer needed. An additional method _AddContour_ is part of the interface, which allows adding contours and (possibly non-convex) holes in an easy way.

## 2. Removed public Mesh constructor
A mesh can be created by the _GenericMesher_ class in the _TriangleNet.Meshing_ namespace. The preferred method is to use one of the extension methods of the _IPolygon_ interface. If you simply want to triangulate a pointset, you now have access to the Delaunay algorithms in the _TriangleNet.Meshing.Algorithm_ namespace.

## 3. Removed public access to Behavior class
Mesh creation can be controlled by the new _ConstraintOptions_ and _QualityOptions_ classes. The _Verbose_ option was moved to the _Log_ class. If you are missing any options, let me know!

## 4. Removed Load method from Mesh class
Use the _Converter_ class in the _TriangleNet.Meshing_ namespace or the _TriangleFormat_ class in the _TriangleNet.IO_ namespace to load meshes.

## 5. Removed Smooth method from Mesh class
Create an instance of the _SimpleSmoother_ class in the _TriangleNet.Smoothing_ namespace and call its _Smooth_ method.

## 6. Missing classes?
Some classes have been renamed, some moved to other namespaces. If you are missing something let me know!

