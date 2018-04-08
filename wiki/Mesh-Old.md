# The Mesh class

## Instance methods

The `Triangulate` method creates a mesh from a given geometry (may be a point set or a PSLG):

```
Mesh mesh = new Mesh();

// Triangulate a geometry stored in a .poly file with Triangle format.
mesh.Triangulate("some-geometry.poly");

// Triangulate a geometry using the InputGeometry class.
var geometry = FileReader.ReadPolyFile("some-geometry.poly");
mesh.Triangulate(geometry);

// Use the FileWriter class to save the mesh.
FileWriter.Write(mesh, "some-mesh.ele");
```

The `Load` method reconstructs a previously generated mesh:

```
// Load a Triangle format mesh file.
mesh.Load("some-mesh.ele");

// Load a mesh using an InputGeometry and a List<ITriangle>.
var geometry = FileReader.ReadPolyFile("some-mesh.poly");
var triangles = FileReader.ReadEleFile("some-mesh.ele");
mesh.Load(geometry, triangles);
```

The `Refine` method refines the current mesh. There are some shorthand overloads, but usually you will use the `Behavior` class (see Behavior [Options](Options.md) page) to set refinement parameters:

```
// Refine by setting an area constraint to half the maximum triangle area.
mesh.Refine(true);

// Get mesh statistics.
var statistic = new Statistic();
statistic.Update(mesh);

// Refine by setting a custom maximum area constraint.
mesh.Refine(statistic.LargestArea / 4);

// Refine by setting a minimum angle constraint using the Behavior property.
mesh.Behavior.MinAngle = 20;
mesh.Refine();
```

The `Renumber` method will renumber the mesh nodes using a given numbering scheme:

```
// Renumber the mesh nodes using the Reverse Cuthill McKee algorithm to
// reduce the adjacency matrix bandwidth.
mesh.Renumber(NodeNumbering.CuthillMcKee);

// Renumber the mesh nodes in linear order.
mesh.Renumber();
```

Finally, the `Check` method returns boolean values, indicating whether the mesh topology is ok and the mesh is Delaunay. If the static property `Behavior.Verbose` is set to `true`, the log will contain more information about what's wrong with the mesh.

```
bool isConsistent, isDelaunay;
mesh.Check(out isConsistent, out isDelaunay);
```

## Public properties

**Warning:** The properties returning an `ICollection` are read-only, so the underlying collections can't be modified.

| Name | Type | Description |
| --- | --- | --- |
| **`Behavior`** | `Behavior` | Gets the mesh Behavior instance. |
| **`Bounds`** | `BoundingBox` | Gets the mesh bounding box. |
| **`Vertices`** | `ICollection<Vertex>` | Gets the mesh vertices. |
| **`Triangles`** | `ICollection<Triangle>` | Gets the mesh triangles. |
| **`Segments`** | `ICollection<Segment>` | Gets the mesh segments. |
| **`Edges`** | `IEnumerable<Edge>` | Gets the mesh edges. |
| **`Holes`** | `IList<Point>` | Gets the mesh holes. |
| **`NumberOfInputPoints`** | `int` | Gets the number of input vertices. |
| **`NumberOfEdges`** | `int` | Gets the number of mesh edges. |
| **`IsPolygon`** | `bool` | Indicates whether the input is a PSLG or a point set. |
| **`CurrentNumbering`** | `NodeNumbering` | Gets the current node numbering. |

