# Creating a Mesh (Beta 4)

Mesh creation in _Triangle.NET_ is controlled by three interfaces:

* `ITriangulator` for triangulating point sets
* `IConstraintMesher` for triangulating polygons
* `IQualityMesher` for creating quality meshes of polygons
All three interfaces are implemented by the `GenericMesher` class (located in the _TriangleNet.Meshing_ namespace).

The following example shows (from left to right) an input polygon, the triangulated point set, the mesh with segments inserted, and the mesh with quality settings applied:

![](Mesh_T.png)

## Triangulation algorithms

The triangulation algorithms in the _TriangleNet.Meshing.Algorithm_ namespace include

* Dwyer (Divide & Conquer) (_the default algorithm_)
* Sweepline (_reasonably fast_)
* Incremental (_slow_)
All classes implement the `ITriangulator` interface, so they can be used to create a mesh from a given point set.

## Triangulating a polygon

The easiest way to triangulate a `Polygon` is to use one of the `Triangulate` extension methods, which are available through the _TriangleNet.Geometry_ namespace.

These methods make use of the `GenericMesher` class. It is recommended that you use the extension methods, unless you want to change the default triangulation algorithm, which can be done using the constructor overloads of the {{GenericMesher}} class.

Here's a simple example, using the _Lake Superior_ polygon file:

```
using TriangleNet.Geometry;
using TriangleNet.IO;
using TriangleNet.Meshing;

public static void Example()
{
    // Load polygon from file.
    var polygon = FileProcessor.Read("superior.poly");

    var options = new ConstraintOptions() { ConformingDelaunay = true };
    var quality = new QualityOptions() { MinimumAngle = 25 };

    // Triangulate the polygon
    var mesh = polygon.Triangulate(options, quality);
}
```

## Generating a structured mesh

The `GenericMesher` provides a static utility function to create structured meshes of rectangular domains:

```
// Create unit square.
var bounds = new Rectangle(-1.0, -1.0, 2.0, 2.0);

// Generate mesh.
var mesh = GenericMesher.StructuredMesh(bounds, 20, 20);
```

