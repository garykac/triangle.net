# InputGeometry
The {{InputGeometry}} class provides a minimal interface to construct geometries consisting of points and segments, which may form a polygon (probably with holes), known as a _planar straight-line graph_ ([PSLG](http://en.wikipedia.org/wiki/Planar_straight-line_graph)). There are methods to add points, segments and holes. Refer to the XML documentation for details.

Additionally the following properties give access to the geometry:
## Public properties

|| Name || Type || Description ||
| **{{Bounds}}** | {{BoundingBox}} | Gets the bounding box of the input geometry. |
| **{{HasSegments}}** | {{bool}} | Indicates whether the geometry should be treated as a PSLG. |
| **{{Count}}** | {{int}} | Gets the number of points. |
| **{{Points}}** | {{IEnumerable<Point>}} | Gets the list of input points. |
| **{{Segments}}** | {{ICollection<Edge>}} | Gets the list of input segments. |
| **{{Holes}}** | {{ICollection<Point>}} | Gets the list of input holes. |
| **{{Regions}}** | {{ICollection<RegionPointer>}} | Gets the list of regions. |
## Example
This example shows how to use the {{InputGeometry}} class to create a square polygon with a hole, described by the following .poly file:

{{
# A box with eight points, no attributes, one boundary marker.
8 2 0 1
# Outer box
  1   0 0   0
  2   3 0   0
  3   3 3   0
  4   0 3   1
# Inner square
  5   1 1   0
  6   2 1   0
  7   2 2   0
  8   1 2   0
# Eight segments with boundary markers.
8 1
# Outer box
  1   1 2   0
  2   2 3   0
  3   3 4   5
  4   4 1   5
# Inner square
  5   5 6   0
  6   6 7   0
  7   7 8   0
  8   8 5   0
# One hole in the middle of the inner square.
1
  1   1.5 1.5
}}

{code:c#}
// Create InputGeometry using overloaded constructor which takes the number 
// of input points as parameter.
var geometry = new InputGeometry(8);

// Add the points. 
geometry.AddPoint(0.0, 0.0); // Index 0
geometry.AddPoint(3.0, 0.0); // Index 1 etc.
geometry.AddPoint(3.0, 3.0);
geometry.AddPoint(0.0, 3.0, 1); // Add a marker.
geometry.AddPoint(1.0, 1.0);
geometry.AddPoint(2.0, 1.0);
geometry.AddPoint(2.0, 2.0);
geometry.AddPoint(1.0, 2.0);

// Add the segments. Notice the zero based indexing (in
// contrast to Triangle's file format).
geometry.AddSegment(0, 1);
geometry.AddSegment(1, 2);
geometry.AddSegment(2, 3, 5); // Add a marker.
geometry.AddSegment(3, 0, 5);
geometry.AddSegment(4, 5);
geometry.AddSegment(5, 6);
geometry.AddSegment(6, 7);
geometry.AddSegment(7, 4);

// Add the hole.
geometry.AddHole(1.5, 1.5);
{code:c#}
## Extending the InputGeometry class
You can use C# extension methods to make the class more verbose:

{code:c#}
/// <summary>
/// Add a polygon ring to the geometry.
/// </summary>
/// <param name="points">List of points which make up the polygon.</param>
/// <param name="mark">Common boundary mark for all segments of the polygon.</param>
public static void AddRing(this InputGeometry geometry, 
        IEnumerable<Point> points, int mark = 0)
{
    // Save the current number of points.
    int N = geometry.Count;

    int m = 0;
    foreach (var pt in points)
    {
        geometry.AddPoint(pt.X, pt.Y, pt.Boundary, pt.Attributes);
        m++;
    }

    for (int i = 0; i < m; i++)
    {
        geometry.AddSegment(N + i, N + ((i + 1) % m), mark);
    }
}

/// <summary>
/// Add a polygon ring to the geometry and make it a hole.
/// </summary>
/// <remarks>
/// WARNING: This works for convex polygons, but not for non-convex regions in general.
/// </remarks>
/// <param name="points">List of points which make up the hole.</param>
/// <param name="mark">Common boundary mark for all segments of the hole.</param>
public static void AddRingAsHole(this InputGeometry geometry, 
        IEnumerable<Point> points, int mark = 0)
{
    // Save the current number of points.
    int N = geometry.Count;

    // Hole coordinates
    double x = 0.0;
    double y = 0.0;

    int m = 0;
    foreach (var pt in points)
    {
        x += pt.X;
        y += pt.Y;

        geometry.AddPoint(pt.X, pt.Y, pt.Boundary, pt.Attributes);
        m++;
    }

    for (int i = 0; i < m; i++)
    {
        geometry.AddSegment(N + i, N + ((i + 1) % m), mark);
    }

    geometry.AddHole(x / m, y / m);
}
{code:c#}

Using the extension methods, the above example will now read

{code:c#}
// Outer square
var outer = new Point[]() {
    new Point(0.0, 0.0),
    new Point(3.0, 0.0),
    new Point(3.0, 3.0),
    new Point(0.0, 3.0, 1),
};

// Inner square
var inner = new Point[]() {
    new Point(1.0, 1.0),
    new Point(2.0, 1.0),
    new Point(2.0, 2.0),
    new Point(1.0, 2.0),
};

var geometry = new InputGeometry(8);
geometry.AddRing(outer, 5);
geometry.AddRingAsHole(inner);
{code:c#}

**WARNING:** The new code does not produce exactly the same input as the original! Do you spot the difference?


