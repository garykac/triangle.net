# Example 10 - Troubleshooting 1

This example will show how to deal with degeneracies on a mesh boundary caused by nearly collinear points.

Assume we have a simple box shape given by a point set and the corresponding mesh. Now let's rotate the box and triangulate the rotated point set:

![](Example%2010_example-10.png)

You would expect the generated meshes to be equivalent (in a topological sense), but you will find that some flat triangles have been created on the boundary.

The problem here is, though the points of the original box are exactly collinear, the rotated points may fail to be so, due to finite precision floating point arithmetics. Some of the edge midpoints might fall slightly inside the convex hull of the box and cause some very thin triangles to be created.

The resulting mesh will be a perfectly valid Delaunay triangulation and Triangle.NET won't detect this kind of degeneracy automatically. The following code will report boundary triangles with a maximum angle near 180 degrees:

```
namespace Examples
{
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using TriangleNet.Geometry;
    using TriangleNet.Meshing;
    using TriangleNet.Tools;

    public class Example10
    {
        public static void Run()
        {
            var pts = new List<Vertex>
            {
                // The 4 corners of the box.
                new Vertex(1.5, 1.0),
                new Vertex(1.5, -1.0),
                new Vertex(-1.5, -1.0),
                new Vertex(-1.5, 1.0),

                // The edge midpoints.
                new Vertex(0.0, 1.0),
                new Vertex(0.0, -1.0),
                new Vertex(1.5, 0.0),
                new Vertex(-1.5, 0.0)
            };

            var r = new Random(78403);

            // The original box.
            var poly = Rotate(pts, 0);

            for (int i = 0; i < 10; i++)
            {
                var mesh = poly.Triangulate();

                var list = CheckBoundary(mesh).ToList();

                if (list.Count > 0)
                {
                    Console.WriteLine("Iteration {0}: found {1} invalid triangle(s).",
                        i, list.Count);

                    foreach (var t in list)
                    {
                        Console.WriteLine("   [{0} {1} {2}]({0}-{1}-{2})",
                            t.GetVertexID(0),
                            t.GetVertexID(1),
                            t.GetVertexID(2));
                    }
                }

                // Random rotation.
                poly = Rotate(pts, Math.PI * r.NextDouble());
            }
        }

        private static IEnumerable<ITriangle> CheckBoundary(IMesh mesh,
            double threshold = 1e-8)
        {
            // We will compare against the squared cosine of the maximum angle.
            threshold = Math.Sqrt(threshold);

            var data = new double[6](6);

            // See Statistic.ComputeAngles():
            //
            // data[0](0) = squared cosine of minimum angle
            // data[1](1) = squared cosine of maximum angle
            // data[2](2) = -1 => maximum angle is obtuse

            foreach (var triangle in mesh.Triangles)
            {
                for (int i = 0; i < 3; i++)
                {
                    var neighbor = triangle.GetNeighbor(i);

                    // Triangle lies on mesh boundary.
                    if (neighbor == null)
                    {
                        Statistic.ComputeAngles(triangle, data);

                        // The squared cosine of the maximum angle will be near
                        // 1.0 only if the maximum angle is near 180 degrees.
                        if (Math.Abs(1.0 - data[1](1)) < threshold)
                        {
                            yield return triangle;
                        }

                        // Next triangle.
                        break;
                    }
                }
            }
        }

        /// <summary>
        /// Rotate given point set around the origin.
        /// </summary>
        private static IPolygon Rotate(List<Vertex> points, double radians)
        {
            var polygon = new Polygon(points.Count);

            int id = 0;

            foreach (var p in points)
            {
                double x = p.X;
                double y = p.Y;

                double xr = Math.Cos(radians) * x - Math.Sin(radians) * y;
                double yr = Math.Sin(radians) * x + Math.Cos(radians) * y;

                polygon.Points.Add(new Vertex(xr, yr) { ID = id++ });
            }

            return polygon;
        }
    }
}
```
