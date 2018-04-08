# Creating a Polygon (Beta 4)

The `IPolygon` interface provides a minimal interface to construct geometries consisting of points and segments, which may form a polygon (probably with holes), known as a _planar straight-line graph_ ([PSLG](http://en.wikipedia.org/wiki/Planar_straight-line_graph)). The following examples will show different ways to create a simple box with a hole.

## Using contours

A contour may constitute an inner or an outer boundary of a polygon. There are two methods for adding a `Contour` to a polygon:
* The `Add(Contour contour, bool hole = false)` method will add the contour and make it a hole, if the boolean `hole` parameter is set to `true`.
* The `Add(Contour contour, Point hole)` method will add the contour as a hole. The `hole` parameter must be a point inside the contour.

```
using TriangleNet.Geometry;

public static void Example()
{
    var p = new Polygon();

    // Add the outer box contour with boundary marker 1.
    p.Add(new Contour(new Vertex[4](4)
    {
        new Vertex(0.0, 0.0, 1),
        new Vertex(3.0, 0.0, 1),
        new Vertex(3.0, 3.0, 1),
        new Vertex(0.0, 3.0, 1)
    }, 1));
        
    // Add the inner box contour with boundary marker 2.
    p.Add(new Contour(new Vertex[4](4)
    {
        new Vertex(1.0, 1.0, 2),
        new Vertex(2.0, 1.0, 2),
        new Vertex(2.0, 2.0, 2),
        new Vertex(1.0, 2.0, 2)
    }, 2)
    , new Point(1.5, 1.5)); // Make it a hole.
}
```

## Using segments

There are two methods for adding a `Segment` to a polygon:
* The `Add(ISegment segment, bool insert = false)` method will add the segment. Additionally, both endpoints will be added, if the boolean `insert` parameter is set to `true`.
* The `Add(ISegment segment, int index)` method will add the segment and one of its endpoints, determined by the `index` parameter.
The following example code makes use of the second overload, using an `index` of 0, meaning that the first endpoint will automatically be added to the polygon `Points` collection:

```
using TriangleNet.Geometry;

public static void Example()
{
    var p = new Polygon();

    var v = new Vertex[4](4)
    {
        new Vertex(0.0, 0.0, 1),
        new Vertex(3.0, 0.0, 1),
        new Vertex(3.0, 3.0, 1),
        new Vertex(0.0, 3.0, 1)
    };

    // Add segments of the outer box.
    p.Add(new Segment(v[0](0), v[1](1), 1), 0);
    p.Add(new Segment(v[1](1), v[2](2), 1), 0);
    p.Add(new Segment(v[2](2), v[3](3), 1), 0);
    p.Add(new Segment(v[3](3), v[0](0), 1), 0);
        
    v = new Vertex[4](4)
    {
        new Vertex(1.0, 1.0, 2),
        new Vertex(2.0, 1.0, 2),
        new Vertex(2.0, 2.0, 2),
        new Vertex(1.0, 2.0, 2)
    };

    // Add segments of the inner box.
    p.Add(new Segment(v[0](0), v[1](1), 2), 0);
    p.Add(new Segment(v[1](1), v[2](2), 2), 0);
    p.Add(new Segment(v[2](2), v[3](3), 2), 0);
    p.Add(new Segment(v[3](3), v[0](0), 2), 0);

    // Add the hole.
    p.Holes.Add(new Point(1.5, 1.5));
}
```

## Using a .poly file

The above geometry can be described by the following .poly file:

```
# A box with a hole: eight points, no attributes, one boundary marker.
8 2 0 1
# Outer box
  1   0.0 0.0   1
  2   3.0 0.0   1
  3   3.0 3.0   1
  4   0.0 3.0   1
# Inner square
  5   1.0 1.0   2
  6   2.0 1.0   2
  7   2.0 2.0   2
  8   1.0 2.0   2
# Eight segments with boundary markers.
8 1
# Outer box
  1   1 2   1
  2   2 3   1
  3   3 4   1
  4   4 1   1
# Inner square
  5   5 6   2
  6   6 7   2
  7   7 8   2
  8   8 5   2
# One hole in the middle of the inner square.
1
  1   1.5 1.5
```

The file can be loaded using the `FileProcessor` class:

```
using TriangleNet.IO;

public static void Example()
{
    // Load polygon from file.
    var polygon = FileProcessor.Read("box.poly");
}
```


